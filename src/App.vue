<script setup lang="ts">
import { ref, onMounted, onUnmounted, nextTick } from "vue";

const mouse = { x: -100, y: -100 };
const pos = { x: -100, y: -100 };
const hovering = ref(false);
const velocity = ref(0);
let frameId: number;

const lerp = (start: number, end: number, factor: number) =>
  start + (end - start) * factor;

const handleMouseMove = (e: MouseEvent) => {
  mouse.x = e.clientX;
  mouse.y = e.clientY;

  const target = e.target as HTMLElement;
  if (
    target?.tagName === "A" ||
    target?.tagName === "BUTTON" ||
    target?.closest("a") ||
    target?.closest("button")
  ) {
    hovering.value = true;
  } else {
    hovering.value = false;
  }
};

const canvasRef = ref<HTMLCanvasElement | null>(null);
const stars = [] as any[];
const initCanvas = () => {
  if (canvasRef.value) {
    const resize = () => {
      if (canvasRef.value) {
        canvasRef.value.width = window.innerWidth;
        canvasRef.value.height = window.innerHeight;
      }
    };
    window.addEventListener("resize", resize);
    resize();

    for (let i = 0; i < 40; i++) {
      stars.push({
        x: Math.random() * window.innerWidth,
        y: Math.random() * window.innerHeight,
        length: Math.random() * 60 + 20,
        speed: Math.random() * 2 + 0.5,
        lx: 0,
        ly: 0,
      });
    }
  }
};

const onFrame = () => {
  pos.x = lerp(pos.x, mouse.x, 0.4);
  pos.y = lerp(pos.y, mouse.y, 0.4);

  const dxMouse = mouse.x - pos.x;
  const dyMouse = mouse.y - pos.y;
  velocity.value = Math.min(
    Math.sqrt(dxMouse * dxMouse + dyMouse * dyMouse) / 50,
    1.5,
  );

  const cursorDot = document.getElementById("cursor-dot");

  if (cursorDot) {
    const angleCursor = Math.atan2(dyMouse, dxMouse);
    const stretch = 1 + velocity.value * 0.2;
    const scaleY = 1 - velocity.value * 0.1;
    cursorDot.style.transform = `translate3d(${pos.x}px, ${pos.y}px, 0) rotate(${angleCursor}rad) scaleX(${stretch}) scaleY(${scaleY})`;
  }

  if (canvasRef.value) {
    const canvas = canvasRef.value;
    const ctx = canvas.getContext("2d");
    if (ctx) {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.lineWidth = 1.5;
      const angle = Math.PI / 3;

      stars.forEach((s) => {
        s.x += Math.cos(angle) * s.speed;
        s.y += Math.sin(angle) * s.speed;

        if (s.x > canvas.width + s.length || s.y > canvas.height + s.length) {
          if (Math.random() > 0.5) {
            s.x = Math.random() * canvas.width;
            s.y = -s.length;
          } else {
            s.x = -s.length;
            s.y = Math.random() * canvas.height;
          }
        }

        const dx = s.x - mouse.x;
        const dy = s.y - mouse.y;
        const dist = Math.sqrt(dx * dx + dy * dy);
        let targetLx = 0,
          targetLy = 0;
        if (dist < 200) {
          const force = (200 - dist) / 200;
          targetLx = (dx / dist) * force * 80;
          targetLy = (dy / dist) * force * 80;
        }

        s.lx = lerp(s.lx, targetLx, 0.1);
        s.ly = lerp(s.ly, targetLy, 0.1);

        const drawX = s.x + s.lx;
        const drawY = s.y + s.ly;

        const grad = ctx.createLinearGradient(
          drawX,
          drawY,
          drawX - Math.cos(angle) * s.length,
          drawY - Math.sin(angle) * s.length,
        );
        grad.addColorStop(0, "rgba(168, 85, 247, 0.4)");
        grad.addColorStop(1, "rgba(168, 85, 247, 0)");

        ctx.strokeStyle = grad;
        ctx.beginPath();
        ctx.moveTo(drawX, drawY);
        ctx.lineTo(
          drawX - Math.cos(angle) * s.length,
          drawY - Math.sin(angle) * s.length,
        );
        ctx.stroke();
      });
    }
  }

  frameId = requestAnimationFrame(onFrame);
};

const handleScroll = () => {
  const scrolled = window.scrollY;
  const parallaxBg = document.getElementById("parallax-bg");
  if (parallaxBg) {
    parallaxBg.style.transform = `translate3d(0, ${scrolled * 0.3}px, 0)`;
  }
};

const setupObservers = () => {
  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          entry.target.classList.add("reveal-active");
        }
      });
    },
    { threshold: 0.15 },
  );

  document.querySelectorAll(".reveal").forEach((el) => observer.observe(el));
};

onMounted(() => {
  window.addEventListener("mousemove", handleMouseMove);
  window.addEventListener("scroll", handleScroll, { passive: true });

  initCanvas();
  frameId = requestAnimationFrame(onFrame);

  nextTick(() => {
    setupObservers();
  });
});

onUnmounted(() => {
  window.removeEventListener("mousemove", handleMouseMove);
  window.removeEventListener("scroll", handleScroll);
  cancelAnimationFrame(frameId);
});

const techStack = [
  {
    category: "Backend",
    items: ["GoLang", "TypeScript", "Luau", "Python", "PHP", "Node.js"],
  },
  { category: "Distributed", items: ["Kafka", "gRPC", "Raft", "Redis"] },
  {
    category: "Databases",
    items: ["PostgreSQL", "Cassandra", "MySQL", "Firebase", "MongoDB"],
  },
  {
    category: "Observability",
    items: ["OpenTelemetry", "Prometheus", "Grafana"],
  },
];

const specialization = [
  {
    area: "High-Concurrency Architecture",
    desc: "Designing stateless compute loops that sustain massive traffic spikes with predictable, low latencies.",
  },
  {
    area: "Microservices & Event-Driven",
    desc: "Decoupling complex bounded contexts using Kafka and gRPC to establish resilient, self-healing system topologies.",
  },
  {
    area: "Performance Tuning & Profiling",
    desc: "Deep diving into runtime flame graphs, catching memory leaks, and tuning garbage collection parameters.",
  },
  {
    area: "Infrastructure & Kubernetes",
    desc: "Crafting robust GitOps delivery pipelines and architecting auto-scaling K8s platforms across multi-region deployments.",
  },
];

const projects = [
  {
    name: "Luaxis",
    problem:
      "No proper ROBLOX courses that had a in-website ROBLOX LUAU runtime and no quality courses on both game development and computers and applicable knowledge in roblox, so we fixed it.",
    architecture:
      "Microservices and Kubernetes to run a custom in-browser Luau runtime, course management, and interactive coding challenges.",
    tech: "GoLang, gRPC, Containerd, Kubernetes, Luau, Zap, Three.js, WASM",
    outcome:
      "Ability to handle 5,000+ users simultaneously running code in-browser with zero downtime and a growing catalog of courses on both game development and computer science topics.",
    demoUrl: "https://luaxis.xyz/",
  },
  {
    name: "Smart Academic Tutor",
    problem:
      "Existing online learning platforms lack personalized, real-time feedback mechanisms, leading to decreased student engagement and suboptimal learning outcomes, as well the pricing is absolutely ridiculous.",
    architecture:
      "Designed a microservices architecture with a central recommendation engine that analyzes student interactions and performance data to provide personalized learning paths and real-time feedback.",
    tech: "TypeScript, Node.js, Kafka, Firebase, OpenAI API, self-hosted AI model",
    outcome:
      "Implemented a prototype that demonstrated a 30% increase in student engagement and a 20% improvement in learning outcomes compared to traditional online learning platforms, as well as affordable pricing.",
    demoUrl: "http://207.180.243.164:6967/smart-academic-tutor/",
  },
];
</script>

<template>
  <div
    class="parallax-wrapper relative bg-zinc-950 text-zinc-50 font-sans selection:bg-purple-900/50"
  >
    <canvas
      ref="canvasRef"
      class="fixed inset-0 w-full h-full z-0 pointer-events-none opacity-70 mix-blend-screen"
    ></canvas>

    <div
      id="cursor-dot"
      class="custom-cursor custom-cursor-dot"
      :class="{ hovering: hovering }"
    ></div>

    <div id="parallax-bg" class="fixed inset-0 pointer-events-none z-0">
      <div
        class="bg-glow w-[600px] h-[600px] bg-purple-600/20 top-[-10%] left-[-10%]"
      ></div>
      <div
        class="bg-glow w-[800px] h-[800px] bg-blue-600/10 top-[40%] right-[-20%]"
      ></div>
    </div>

    <div class="relative z-10 container mx-auto px-6 md:px-12 lg:px-24">
      <section class="min-h-[90vh] flex py-20 reveal relative">
        <div
          class="w-full md:w-3/4 flex flex-col justify-center z-10 bg-zinc-950/20 rounded-[3rem] backdrop-blur-[2px] -mx-6 px-6 lg:-mx-12 lg:px-12 shadow-black/5"
        >
          <div
            class="inline-flex items-center gap-2 px-3 py-1 rounded-full bg-zinc-900/80 border border-zinc-800 text-sm text-zinc-400 mb-8 w-fit backdrop-blur-md"
          >
            <span
              class="w-2 h-2 rounded-full bg-green-500 animate-pulse shadow-[0_0_8px_rgba(34,197,94,0.6)]"
            ></span>
            Open for opportunities
          </div>
          <h1
            class="text-5xl md:text-7xl lg:text-8xl font-black tracking-tighter mb-6 text-glow"
          >
            Mihael
            <span
              class="bg-gradient-to-b from-zinc-100 to-zinc-500 bg-clip-text text-transparent"
              >Pleško</span
            >
          </h1>
          <h2
            class="text-2xl md:text-3xl text-zinc-400 font-medium mb-8 max-w-2xl leading-snug"
          >
            Backend Engineer specializing in Distributed Systems & Highly
            Concurrent Architecture.
          </h2>
          <p class="text-zinc-500 text-lg mb-12 max-w-xl">
            I design software that doesn't break under pressure. Focused on
            system observability, fault-tolerance, and resilient scale. I
            provide results, not
            <span class="text-purple-400 font-bold">bullsh*t</span>.
          </p>
          <div class="flex gap-4">
            <a
              href="#projects"
              class="px-6 py-3 bg-zinc-100 text-zinc-950 font-semibold rounded-lg hover:bg-white hover:scale-105 transition-all shadow-lg hover:shadow-zinc-100/30"
            >
              View Work
            </a>
            <a
              href="#contact"
              class="px-6 py-3 border border-zinc-800 bg-zinc-950/80 hover:bg-zinc-800 text-zinc-300 font-semibold rounded-lg transition-colors backdrop-blur-md"
            >
              Get in touch
            </a>
          </div>
        </div>

        <div
          class="absolute bottom-10 left-1/2 -translate-x-1/2 flex flex-col items-center gap-2 opacity-80 hover:opacity-100 transition-opacity animate-[pulse_3s_ease-in-out_infinite]"
        >
          <span
            class="text-xs font-semibold tracking-widest uppercase text-zinc-300"
            >Scroll</span
          >
          <svg
            class="w-5 h-5 text-zinc-200 animate-bounce"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M19 14l-7 7m0 0l-7-7m7 7V3"
            ></path>
          </svg>
        </div>
      </section>

      <section class="py-32 border-y border-zinc-900/50 reveal">
        <div
          class="flex flex-col md:flex-row justify-between items-start gap-12 lg:gap-24 max-w-6xl"
        >
          <div class="flex flex-col gap-2 group">
            <div
              class="text-7xl lg:text-8xl font-black text-white tracking-tighter group-hover:text-purple-400 transition-colors"
            >
              2<span class="text-5xl lg:text-6xl text-zinc-600">+</span>
            </div>
            <div
              class="text-zinc-500 font-bold text-sm tracking-widest uppercase"
            >
              Years of Commercial/Corporate Experience
            </div>
            <div class="text-zinc-400 mt-2 max-w-xs text-lg">
              Architecting resilient schemas inside fast-paced engineering
              teams.
            </div>
          </div>

          <div class="flex flex-col gap-2 group">
            <div
              class="text-7xl lg:text-8xl font-black text-white tracking-tighter group-hover:text-purple-400 transition-colors"
            >
              99<span class="text-5xl lg:text-6xl text-purple-500">.99%</span>
            </div>
            <div
              class="text-zinc-500 font-bold text-sm tracking-widest uppercase"
            >
              Service Uptime
            </div>
            <div class="text-zinc-400 mt-2 max-w-xs text-lg">
              Maintained securely across volatile distributed microservices.
            </div>
          </div>

          <div class="flex flex-col gap-2 group">
            <div
              class="text-7xl lg:text-8xl font-black text-white tracking-tighter group-hover:text-purple-400 transition-colors"
            >
              100<span class="text-5xl lg:text-6xl text-purple-500">k</span>
            </div>
            <div
              class="text-zinc-500 font-bold text-sm tracking-widest uppercase"
            >
              Events / Second
            </div>
            <div class="text-zinc-400 mt-2 max-w-xs text-lg">
              Peaks processed flawlessly via scalable runtime integrations.
            </div>
          </div>
        </div>
      </section>

      <section class="py-32 reveal">
        <h3 class="text-4xl font-bold mb-12 tracking-tight">
          Technology Target
        </h3>
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
          <div
            v-for="(group, i) in techStack"
            :key="i"
            class="border border-zinc-800/80 bg-zinc-950/50 backdrop-blur-md p-8 rounded-3xl hover:border-zinc-700/80 transition-colors shadow-xl"
          >
            <h4
              class="text-zinc-100 font-semibold mb-6 flex items-center gap-3"
            >
              <div class="w-1.5 h-1.5 rounded-full bg-purple-500"></div>
              {{ group.category }}
            </h4>
            <div class="flex flex-wrap gap-2">
              <span
                v-for="item in group.items"
                :key="item"
                class="px-3 py-1.5 text-sm bg-zinc-900 border border-zinc-800 rounded-lg text-zinc-300 shadow-sm hover:bg-zinc-800 hover:text-white transition-colors"
              >
                {{ item }}
              </span>
            </div>
          </div>
        </div>
      </section>

      <section class="py-32 reveal">
        <div class="grid lg:grid-cols-2 gap-16 lg:gap-32">
          <div class="sticky top-24 self-start">
            <h3 class="text-4xl font-bold mb-8 tracking-tight">
              Engineering Philosophy
            </h3>
            <div class="space-y-6 text-zinc-400 text-xl leading-relaxed">
              <p>
                My fundamental engineering belief is that architecture should be
                as simple as possible, but no simpler. I prioritize stateless
                node operations and push complexity to persistent data
                boundaries only when necessary.
              </p>
              <p>
                Readability counts, but at scale,
                <em>mechanical sympathy</em> prevents outages. I dive deep into
                system fundamentals because knowing exactly how the database
                engine performs under pressure beats blindly scaling hardware.
              </p>
            </div>
          </div>

          <div class="space-y-8 lg:mt-8">
            <div
              v-for="(skill, i) in specialization"
              :key="i"
              class="border-l-2 border-purple-500/20 pl-8 hover:border-purple-500 transition-colors duration-500 group py-2"
            >
              <h4
                class="text-2xl font-bold text-zinc-200 mb-3 group-hover:text-white"
              >
                {{ skill.area }}
              </h4>
              <p class="text-zinc-500 text-lg leading-relaxed">
                {{ skill.desc }}
              </p>
            </div>
          </div>
        </div>
      </section>

      <section id="projects" class="py-32 border-t border-zinc-900/50 reveal">
        <h3 class="text-4xl font-bold mb-16 tracking-tight">
          Contributed & Personal Projects
        </h3>
        <div class="space-y-8">
          <div
            v-for="(proj, i) in projects"
            :key="i"
            class="border border-zinc-800/80 bg-zinc-950/40 backdrop-blur-md hover:bg-zinc-900/40 transition-all duration-500 p-8 lg:p-12 rounded-[2rem] group flex flex-col md:flex-row gap-8 lg:gap-16 shadow-2xl"
          >
            <div class="md:w-1/3 flex flex-col">
              <h4
                class="text-3xl font-bold text-zinc-100 group-hover:text-purple-400 transition-colors"
              >
                {{ proj.name }}
              </h4>
              <div
                class="mt-4 text-xs tracking-widest text-zinc-500 uppercase font-bold"
              >
                {{ proj.tech }}
              </div>
            </div>

            <div class="md:w-2/3 flex flex-col gap-6">
              <div class="grid md:grid-cols-2 gap-8">
                <div>
                  <h5 class="text-zinc-200 font-semibold mb-2">Problem</h5>
                  <p class="text-zinc-400 text-[1.05rem] leading-relaxed">
                    {{ proj.problem }}
                  </p>
                </div>
                <div>
                  <h5 class="text-zinc-200 font-semibold mb-2">Architecture</h5>
                  <p class="text-zinc-400 text-[1.05rem] leading-relaxed">
                    {{ proj.architecture }}
                  </p>
                </div>
              </div>

              <div
                class="mt-4 pt-6 border-t border-zinc-800/50 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-6"
              >
                <div>
                  <span class="text-green-500 font-semibold">Outcome:</span>
                  <span class="text-zinc-300 ml-3 text-[1.05rem]">{{
                    proj.outcome
                  }}</span>
                </div>
                <a
                  v-if="proj.demoUrl"
                  :href="proj.demoUrl"
                  target="_blank"
                  class="shrink-0 flex items-center gap-2 px-6 py-3 bg-purple-600/10 hover:bg-purple-600/20 text-purple-400 font-semibold rounded-xl transition-all border border-purple-500/20 whitespace-nowrap shadow-lg shadow-purple-900/20"
                >
                  <svg
                    class="w-4 h-4"
                    fill="none"
                    viewBox="0 0 24 24"
                    stroke="currentColor"
                    stroke-width="2"
                  >
                    <path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14"
                    />
                  </svg>
                  View Demo
                </a>
              </div>
            </div>
          </div>
        </div>
      </section>

      <section class="py-32 reveal">
        <h3 class="text-4xl font-bold mb-16 tracking-tight">Career Timeline</h3>
        <div class="relative pl-10 border-l border-zinc-800 space-y-16">
          <div class="relative group">
            <div
              class="absolute -left-[49.5px] top-1.5 w-5 h-5 rounded-full bg-zinc-950 border-2 border-purple-500 group-hover:scale-125 transition-transform shadow-[0_0_8px_rgba(168,85,247,0.5)]"
            ></div>
            <h4 class="text-3xl font-bold text-zinc-100">
              Senior Backend Engineer
            </h4>
            <div
              class="text-purple-400/80 mb-6 text-sm font-bold tracking-widest uppercase mt-2"
            >
              SomethingStudio • 2024 — 2026
            </div>
            <p class="text-zinc-400 text-lg max-w-3xl leading-relaxed">
              Developed in-game backend systems capable of processing
              <span class="text-purple-400 font-bold">100+ event</span> updates
              per second across distributed game servers while maintaining
              consistent state synchronization. Built and maintained the web
              service provided by SomethingStudio, allowing developers to manage
              ROBLOX games and in-game events across multiple servers, achieving
              <span class="text-purple-400 font-bold"
                >99.5-99.9% successful</span
              >
              event delivery reliability and reducing manual operational actions
              by roughly 40% through automation.
            </p>
          </div>
          <div class="relative group">
            <div
              class="absolute -left-[49.5px] top-1.5 w-5 h-5 rounded-full bg-zinc-950 border-2 border-zinc-600 group-hover:scale-125 group-hover:border-zinc-400 transition-all"
            ></div>
            <h4 class="text-3xl font-bold text-zinc-300">Backend Engineer</h4>
            <div
              class="text-zinc-500 mb-6 text-sm font-bold tracking-widest uppercase mt-2"
            >
              Blackout Innovations • 2024 — 2025
            </div>
            <p class="text-zinc-400 text-lg max-w-3xl leading-relaxed">
              Assisted in applying my expertise in gameplay programming and game
              server infrastructure. Designed and implemented multiple highly
              concurrent backend services within the game environment,
              supporting
              <span class="text-purple-400 font-bold"
                >200-1,000+ concurrent players</span
              >
              per experience session and handling
              <span class="text-purple-400 font-bold"
                >50-150 gameplay events</span
              >
              per second during peak activity. Improved server responsiveness by
              reducing average event processing latency from
              <span class="text-purple-400 font-bold">~120 ms</span> to
              <span class="text-purple-400 font-bold">40-60 ms</span>, resulting
              in smoother gameplay interactions and fewer desynchronization
              issues. Contributed to service stability improvements that reduced
              server-side errors by approximately
              <span class="text-purple-400 font-bold">25-35%</span> across live
              sessions.
            </p>
          </div>
        </div>
      </section>

      <section
        id="contact"
        class="py-32 reveal text-center border-t border-zinc-900/50"
      >
        <h3
          class="text-5xl md:text-7xl font-black mb-8 text-glow tracking-tight mt-12"
        >
          Signal Received.
        </h3>
        <p
          class="text-zinc-400 text-xl mb-12 max-w-2xl mx-auto leading-relaxed"
        >
          I'm currently engaged with exciting workflows, but my inbox is always
          open regarding resilient architecture, distributed systems, or coffee.
        </p>
        <a
          href="mailto:mihahrv@pm.me"
          class="inline-block px-10 py-5 bg-zinc-100 text-zinc-950 text-lg font-bold rounded-2xl hover:bg-white hover:-translate-y-1 transition-all mb-20 shadow-xl shadow-zinc-100/10"
        >
          mihahrv@pm.me
        </a>

        <div class="flex justify-center gap-8">
          <a
            href="https://www.linkedin.com/in/mihael-ple%C5%A1ko-5a612233a/"
            target="_blank"
            class="w-14 h-14 rounded-full border border-zinc-800 bg-zinc-900/50 flex items-center justify-center hover:bg-zinc-800 hover:border-zinc-700 hover:text-white transition-all text-zinc-400 shadow-lg"
          >
            <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24">
              <path
                d="M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433c-1.144 0-2.063-.926-2.063-2.065 0-1.138.92-2.063 2.063-2.063 1.14 0 2.064.925 2.064 2.063 0 1.139-.925 2.065-2.064 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.222 0h.003z"
              />
            </svg>
          </a>
        </div>
      </section>

      <footer
        class="py-12 border-t border-zinc-900 text-center text-zinc-600 text-sm"
      >
        <p>&copy; 2026&copy; Mihael Pleško</p>
      </footer>
    </div>
  </div>
</template>
