<template>
  <div
    ref="containerRef"
    class="w-full h-[500px] md:h-[600px] relative mt-16 md:mt-0 flex items-center justify-center cursor-crosshair group z-20"
  ></div>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from "vue";
import * as THREE from "three";
import { AsciiEffect } from "three/examples/jsm/effects/AsciiEffect.js";

const containerRef = ref<HTMLElement | null>(null);
let effectParams = {
  resolution: 0.15,
  scale: 1,
  color: "#c084fc", // purple-400
  bgColor: "transparent",
  invert: true,
};

onMounted(() => {
  if (!containerRef.value) return;

  const width = containerRef.value.clientWidth;
  const height = containerRef.value.clientHeight;

  const scene = new THREE.Scene();

  const camera = new THREE.PerspectiveCamera(45, width / height, 0.1, 1000);
  camera.position.z = 4;
  camera.position.y = 0.5;

  const renderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });
  renderer.setSize(width, height);
  renderer.setClearColor(0x000000, 0);

  let effect = new AsciiEffect(renderer, " .:-+*=%@#%", { invert: true });
  effect.setSize(width, height);
  effect.domElement.style.color = effectParams.color;
  effect.domElement.style.backgroundColor = effectParams.bgColor;
  effect.domElement.style.overflow = "hidden";
  effect.domElement.style.pointerEvents = "none";

  containerRef.value.appendChild(effect.domElement);

  const group = new THREE.Group();
  scene.add(group);

  const monitorGeo = new THREE.BoxGeometry(2.4, 1.8, 1.5);
  const screenGeo = new THREE.PlaneGeometry(2.2, 1.6, 64, 64);

  const monitorMat = new THREE.MeshStandardMaterial({
    color: 0x111111,
    roughness: 0.8,
  });
  const screenMat = new THREE.MeshStandardMaterial({
    color: 0xa855f7,
    roughness: 0.2,
    metalness: 0.8,
  });

  const monitor = new THREE.Mesh(monitorGeo, monitorMat);
  group.add(monitor);

  const screen = new THREE.Mesh(screenGeo, screenMat);
  screen.position.z = 0.76;
  group.add(screen);

  const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
  scene.add(ambientLight);

  const mainLight = new THREE.PointLight(0xffffff, 20);
  mainLight.position.set(5, 5, 5);
  scene.add(mainLight);

  const purpleLight = new THREE.PointLight(0xa855f7, 30);
  purpleLight.position.set(-3, -2, 4);
  scene.add(purpleLight);

  const positionAttribute = screenGeo.attributes.position;
  const originalVertices = new Float32Array(positionAttribute.count * 3);
  for (let i = 0; i < positionAttribute.count; i++) {
    originalVertices[i * 3] = positionAttribute.getX(i);
    originalVertices[i * 3 + 1] = positionAttribute.getY(i);
    originalVertices[i * 3 + 2] = positionAttribute.getZ(i);
  }

  const raycaster = new THREE.Raycaster();
  const mouse = new THREE.Vector2(-100, -100);
  let isHovering = false;

  const handleMouseMove = (event: MouseEvent) => {
    if (!containerRef.value) return;
    const rect = containerRef.value.getBoundingClientRect();
    mouse.x = ((event.clientX - rect.left) / rect.width) * 2 - 1;
    mouse.y = -((event.clientY - rect.top) / rect.height) * 2 + 1;
    isHovering = true;
  };

  const handleMouseLeave = () => {
    isHovering = false;
    mouse.set(-100, -100);
  };

  containerRef.value.addEventListener("mousemove", handleMouseMove);
  containerRef.value.addEventListener("mouseleave", handleMouseLeave);

  const handleResize = () => {
    if (!containerRef.value) return;
    const w = containerRef.value.clientWidth;
    const h = containerRef.value.clientHeight;
    camera.aspect = w / h;
    camera.updateProjectionMatrix();
    renderer.setSize(w, h);
    effect.setSize(w, h);
  };

  window.addEventListener("resize", handleResize);

  const clock = new THREE.Clock();
  let animationId: number;

  let hoverIntensity = 0;

  const targetRotation = new THREE.Vector2();
  const currentRotation = new THREE.Vector2();

  const animate = () => {
    animationId = requestAnimationFrame(animate);
    const time = clock.getElapsedTime();

    targetRotation.y = Math.sin(time * 0.5) * 0.3;
    targetRotation.x = Math.sin(time * 0.3) * 0.1;
    group.position.y = Math.sin(time * 1.5) * 0.05;

    if (isHovering) {
      hoverIntensity += (1 - hoverIntensity) * 0.1;
      targetRotation.y -= mouse.x * 0.5;
      targetRotation.x += mouse.y * 0.5;
    } else {
      hoverIntensity += (0 - hoverIntensity) * 0.1;
    }

    currentRotation.lerp(targetRotation, 0.1);
    group.rotation.x = currentRotation.x;
    group.rotation.y = currentRotation.y;

    raycaster.setFromCamera(mouse, camera);
    const intersects = raycaster.intersectObject(screen);

    let intersectPoint: THREE.Vector3 | null = null;
    if (intersects.length > 0) {
      intersectPoint = screen.worldToLocal(intersects[0].point.clone());
    }

    const pos = screenGeo.attributes.position;
    for (let i = 0; i < pos.count; i++) {
      const ix = originalVertices[i * 3];
      const iy = originalVertices[i * 3 + 1];
      const iz = originalVertices[i * 3 + 2];

      let targetZ = iz;

      targetZ += Math.sin(ix * 4 + time * 3) * 0.03;
      targetZ += Math.cos(iy * 5 + time * 2) * 0.03;

      if (intersectPoint && isHovering) {
        const dx = ix - intersectPoint.x;
        const dy = iy - intersectPoint.y;
        const dist = Math.sqrt(dx * dx + dy * dy);
        const radius = 0.5;
        if (dist < radius) {
          const curve = Math.cos((dist / radius) * (Math.PI / 2));
          targetZ += curve * 0.3 * hoverIntensity;
        }
      }

      const currentZ = pos.getZ(i);
      pos.setZ(i, currentZ + (targetZ - currentZ) * 0.15);
    }
    pos.needsUpdate = true;
    screenGeo.computeVertexNormals();

    effect.render(scene, camera);
  };

  animate();

  onBeforeUnmount(() => {
    window.removeEventListener("resize", handleResize);
    if (containerRef.value) {
      containerRef.value.removeEventListener("mousemove", handleMouseMove);
      containerRef.value.removeEventListener("mouseleave", handleMouseLeave);
      if (effect.domElement) {
        containerRef.value.removeChild(effect.domElement);
      }
    }
    cancelAnimationFrame(animationId);
    renderer.dispose();
    monitorGeo.dispose();
    monitorMat.dispose();
    screenGeo.dispose();
    screenMat.dispose();
  });
});
</script>

<style scoped></style>
