<script setup lang="ts">
const techStack = [
  {
    category: "Backend",
    items: ["GoLang", "TypeScript", "Luau", "Python", "PHP", "Node.js"],
  },
  {
    category: "Frontend",
    items: ["Vue", "React", "HTML", "CSS", "Tailwind"],
  },
  {
    category: "Databases",
    items: ["PostgreSQL", "Cassandra", "MySQL", "Firebase", "MongoDB"],
  },
  {
    category: "Infrastructure",
    items: ["Kubernetes", "Kafka", "gRPC", "Docker", "Linux"],
  },
];

const projects = [
  {
    name: "Luaxis",
    problem:
      "There were no practical Roblox courses with an in-browser Luau runtime.",
    architecture:
      "We built microservices and used Kubernetes to run a custom in-browser Luau runtime. We implemented course management and interactive coding challenges.",
    tech: "GoLang, Containerd, Kubernetes, Luau, Zap, Three.js, WASM",
    outcome:
      "The system handles 160+ simultaneous users. Uptime remains at 99.98%.",
    demoUrl: "https://luaxis.xyz/",
    isDemo: false,
  },
  {
    name: "Smart Academic Tutor",
    problem:
      "Online learning platforms lacked real-time feedback. Pricing was too high.",
    architecture:
      "I designed a microservices architecture. I built a central recommendation engine. The engine analyzes student interactions to provide personalized learning paths.",
    tech: "TypeScript, Node.js, Kafka, Firebase, OpenAI API",
    outcome:
      "Student engagement increased by 30%. Learning outcomes improved by 20%. The platform is cost-effective.",
    demoUrl: "http://207.180.243.164:6967/smart-academic-tutor/",
    isDemo: true,
  },
];

const experience = [
  {
    role: "Senior Backend Engineer",
    company: "SomethingStudio",
    period: "2024 - 2026",
    bullets: [
      "Developed in-game backend systems.",
      "Processed 100+ event updates per second.",
      "Built web services to manage game events.",
      "Achieved 99.9% successful event delivery.",
      "Reduced manual operations by 40%.",
    ],
  },
  {
    role: "Backend Engineer",
    company: "Blackout Innovations",
    period: "2023 - 2024",
    bullets: [
      "Designed highly concurrent backend services.",
      "Supported 150+ concurrent players.",
      "Handled 150 gameplay events per second.",
      "Reduced processing latency from 120ms to 40ms.",
      "Decreased server-side errors by 35%.",
    ],
  },
];
</script>

<template>
  <div class="min-h-screen selection:bg-purple-500 selection:text-white">
    <header class="relative px-6 py-32 md:px-12 lg:px-24 border-editorial-b overflow-hidden">
      <div class="absolute inset-0 pointer-events-none bg-[linear-gradient(to_right,#ffffff0a_1px,transparent_1px),linear-gradient(to_bottom,#ffffff0a_1px,transparent_1px)] bg-[size:4rem_4rem] [mask-image:radial-gradient(ellipse_60%_50%_at_50%_0%,#000_70%,transparent_100%)] z-0"></div>
      
      <div class="absolute top-0 left-1/2 -translate-x-1/2 w-[800px] h-[400px] bg-purple-500/20 blur-[120px] rounded-full pointer-events-none z-0 mix-blend-screen"></div>

      <div class="relative z-10 max-w-7xl mx-auto flex flex-col lg:flex-row justify-between items-center lg:items-start gap-12 lg:gap-24">
        
        <div class="flex-1 w-full">
          <h1 class="text-6xl md:text-8xl lg:text-9xl font-serif text-white tracking-tighter mb-8 leading-none">
            Mihael<br />Pleško.
          </h1>
          <h2 class="text-3xl md:text-5xl font-serif text-zinc-400 leading-tight mb-8">
            Website Developer.
          </h2>
          <p class="text-lg md:text-xl text-zinc-300 max-w-md font-sans leading-relaxed mb-12">
            You need a reliable web application. I build frontends and backends. My code handles high traffic without breaking. I deliver functional results.
          </p>
          <a href="#projects" class="inline-flex items-center justify-center px-8 py-4 bg-white text-black font-bold text-lg hover:bg-zinc-200 transition-colors uppercase tracking-widest text-sm">
            Take a look
          </a>
        </div>

        <div class="flex-1 w-full grid grid-cols-1 sm:grid-cols-2 gap-6 pt-8 lg:pt-0">
          <div class="border-editorial p-6 bg-zinc-950/40 backdrop-blur-sm">
            <span class="text-4xl md:text-5xl font-serif text-white block mb-2">2+</span>
            <span class="text-xs tracking-widest text-zinc-500 uppercase font-bold block mb-2">Years</span>
            <span class="text-zinc-400 text-sm">Commercial Experience</span>
          </div>
          <div class="border-editorial p-6 bg-zinc-950/40 backdrop-blur-sm">
            <span class="text-4xl md:text-5xl font-serif text-white block mb-2">99.99%</span>
            <span class="text-xs tracking-widest text-zinc-500 uppercase font-bold block mb-2">Uptime</span>
            <span class="text-zinc-400 text-sm">Maintained services</span>
          </div>
          <div class="border-editorial p-6 bg-zinc-950/40 backdrop-blur-sm">
            <span class="text-4xl md:text-5xl font-serif text-white block mb-2">100k</span>
            <span class="text-xs tracking-widest text-zinc-500 uppercase font-bold block mb-2">Events / Sec</span>
            <span class="text-zinc-400 text-sm">Processed flawlessly</span>
          </div>
          <div class="border-editorial p-6 bg-zinc-950/40 backdrop-blur-sm flex flex-col justify-center">
            <div class="flex items-center gap-3 mb-2">
              <span class="w-3 h-3 rounded-full bg-green-500 animate-pulse"></span>
              <span class="text-white font-bold text-lg">Available</span>
            </div>
            <span class="text-zinc-400 text-sm">Open for new work</span>
          </div>
        </div>
      </div>
    </header>

    <main class="max-w-7xl mx-auto px-6 md:px-12 lg:px-24">
      
      <div class="grid grid-cols-1 lg:grid-cols-12 gap-16 py-32 border-editorial-b">
        <div class="lg:col-span-5">
          <h3 class="text-4xl md:text-5xl font-serif text-white mb-8">Engineering Philosophy.</h3>
          <div class="text-lg text-zinc-400 space-y-6 font-sans">
            <p>Architecture must be simple.</p>
            <p>I prioritize stateless operations. I push complexity to persistent data boundaries only when necessary.</p>
            <p>Readability matters. Understanding system fundamentals prevents outages.</p>
            <p>Knowing database engine performance beats scaling hardware.</p>
          </div>
        </div>
        
        <div class="lg:col-span-6 lg:col-start-7">
          <h3 class="text-4xl md:text-5xl font-serif text-white mb-8">Technology Stack.</h3>
          <div class="grid grid-cols-1 sm:grid-cols-2 gap-8">
            <div v-for="group in techStack" :key="group.category" class="border-editorial p-6">
              <h4 class="text-white font-bold mb-4 uppercase tracking-wider text-sm">{{ group.category }}</h4>
              <ul class="space-y-2 text-zinc-400">
                <li v-for="item in group.items" :key="item">{{ item }}</li>
              </ul>
            </div>
          </div>
        </div>
      </div>

      <section class="py-32 border-editorial-b">
        <h3 class="text-5xl md:text-7xl font-serif text-white mb-16">Experience.</h3>
        <div class="space-y-16">
          <div v-for="job in experience" :key="job.company" class="grid grid-cols-1 md:grid-cols-12 gap-8 md:gap-16">
            <div class="md:col-span-4">
              <h4 class="text-2xl text-white font-serif">{{ job.role }}</h4>
              <div class="text-purple-400 mt-2 font-bold">{{ job.company }}</div>
              <div class="text-zinc-500 text-sm mt-1">{{ job.period }}</div>
            </div>
            <div class="md:col-span-8">
              <ul class="space-y-3 text-lg text-zinc-400 list-disc list-inside marker:text-zinc-700">
                <li v-for="bullet in job.bullets" :key="bullet">{{ bullet }}</li>
              </ul>
            </div>
          </div>
        </div>
      </section>

      <section id="projects" class="py-32">
        <h3 class="text-5xl md:text-7xl font-serif text-white mb-16">Projects.</h3>
        <div class="space-y-16">
          <div v-for="proj in projects" :key="proj.name" class="border-editorial p-8 md:p-12">
            <div class="flex flex-col lg:flex-row justify-between items-start lg:items-center gap-6 mb-12">
              <div>
                <h4 class="text-3xl md:text-4xl font-serif text-white">{{ proj.name }}</h4>
                <div class="text-sm font-bold text-zinc-500 tracking-widest uppercase mt-4">{{ proj.tech }}</div>
              </div>
              <a v-if="proj.demoUrl" :href="proj.demoUrl" target="_blank" class="px-6 py-3 border border-zinc-700 text-white hover:bg-white hover:text-black transition-colors duration-200 uppercase tracking-widest text-sm font-bold">
                {{ proj.isDemo ? "View Demo" : "View Product" }}
              </a>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-12 mb-8">
              <div>
                <h5 class="text-white uppercase tracking-wider text-sm font-bold mb-4">Problem</h5>
                <p class="text-zinc-400 text-lg leading-relaxed">{{ proj.problem }}</p>
              </div>
              <div>
                <h5 class="text-white uppercase tracking-wider text-sm font-bold mb-4">Architecture</h5>
                <p class="text-zinc-400 text-lg leading-relaxed">{{ proj.architecture }}</p>
              </div>
            </div>
            
            <div class="pt-8 border-editorial-t">
              <span class="text-white uppercase tracking-wider text-sm font-bold mr-4">Outcome:</span>
              <span class="text-zinc-400 text-lg">{{ proj.outcome }}</span>
            </div>
          </div>
        </div>
      </section>
    </main>

    <footer class="px-6 py-32 md:px-12 lg:px-24 border-editorial-t bg-zinc-950 text-center">
      <h3 class="text-6xl md:text-8xl font-serif text-white mb-8">Contact Me.</h3>
      <p class="text-xl text-zinc-400 mb-12 max-w-lg mx-auto">
        My inbox is open for new work. 
      </p>
      <a href="mailto:mihahrv@pm.me" class="inline-block px-8 py-4 bg-white text-black font-bold text-lg hover:bg-zinc-200 transition-colors">
        mihahrv@pm.me
      </a>
      
      <div class="mt-32 text-zinc-600 text-sm flex flex-col items-center gap-4">
        <a href="https://www.linkedin.com/in/mihael-ple%C5%A1ko-5a612233a/" target="_blank" class="link-hover text-zinc-400 hover:text-white transition-colors">
          LinkedIn
        </a>
        <p>&copy; 2026 Mihael Pleško. All rights reserved.</p>
      </div>
    </footer>
  </div>
</template>
