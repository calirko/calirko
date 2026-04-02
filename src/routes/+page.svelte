<script lang="ts">
   import { onMount } from "svelte";

   type SectionKind = "profile" | "about" | "projects" | "contact";

   const profile = {
      fullName: "Calirko",
      username: "@calirko",
      title: "Developer Portfolio",
      readme: `hai, i like doing things on my puter`,
   };

   const aboutColumns = {
      left: [
         "I build software with a preference for strict structure, plain visual language, and predictable systems.",
         "This portfolio is intentionally minimal: grayscale palette, rigid borders, and layout hierarchy driven by geometry.",
         "My focus is on maintainable code, clear interfaces, and reliable deployment pipelines.",
      ],
      right: [
         "I work across frontend and backend with a strong emphasis on type safety and composable architecture.",
         "I value documentation that is concise and operational, helping teams move quickly without sacrificing quality.",
         "Outside of implementation, I prioritize review discipline, test coverage, and explicit technical decision making.",
      ],
   };

   const projects = [
      {
         name: "Account Dashboard",
         description:
            "Structured account view with server-side data boundaries and granular UI states.",
         stack: ["SvelteKit", "TypeScript", "TailwindCSS"],
      },
      {
         name: "Service API Core",
         description:
            "Modular API foundation designed for strict contracts and resilient handlers.",
         stack: ["Node.js", "Zod", "PostgreSQL"],
      },
      {
         name: "Container Platform",
         description:
            "Container-first deployment blueprint with controlled networking and reproducible builds.",
         stack: ["Docker", "Compose", "CI/CD"],
      },
      {
         name: "Realtime Monitor",
         description:
            "Internal telemetry panel for service health, throughput trends, and incident diagnostics.",
         stack: ["WebSockets", "Svelte", "Prometheus"],
      },
   ];

   const contacts = [
      { label: "Email", value: "contact@calirko.dev" },
      { label: "GitHub", value: "github.com/calirko" },
      { label: "LinkedIn", value: "linkedin.com/in/calirko" },
   ];

   const sections: { id: string; title: string; kind: SectionKind }[] = [
      { id: "profile", title: "Profile", kind: "profile" },
      { id: "about", title: "About Me", kind: "about" },
      { id: "projects", title: "Projects", kind: "projects" },
      { id: "contact", title: "Contact", kind: "contact" },
   ];

   let scrollContainer: HTMLDivElement | null = null;
   let currentX = 0;
   let targetX = 0;
   let rafId = 0;

   const clamp = (value: number, min: number, max: number): number =>
      Math.max(min, Math.min(max, value));

   const animateScroll = (): void => {
      if (!scrollContainer) return;

      currentX += (targetX - currentX) * 0.14;
      if (Math.abs(targetX - currentX) < 0.5) {
         currentX = targetX;
      }

      scrollContainer.scrollLeft = currentX;
      rafId = window.requestAnimationFrame(animateScroll);
   };

   onMount(() => {
      if (!scrollContainer) return;

      currentX = scrollContainer.scrollLeft;
      targetX = scrollContainer.scrollLeft;

      const handleWheel = (event: WheelEvent): void => {
         event.preventDefault();
         if (!scrollContainer) return;

         const axisDelta =
            Math.abs(event.deltaY) > Math.abs(event.deltaX)
               ? event.deltaY
               : event.deltaX;
         const maxScroll =
            scrollContainer.scrollWidth - scrollContainer.clientWidth;
         targetX = clamp(targetX + axisDelta, 0, maxScroll);
      };

      const handleResize = (): void => {
         if (!scrollContainer) return;
         const maxScroll =
            scrollContainer.scrollWidth - scrollContainer.clientWidth;
         targetX = clamp(targetX, 0, maxScroll);
         currentX = clamp(currentX, 0, maxScroll);
      };

      scrollContainer.addEventListener("wheel", handleWheel, {
         passive: false,
      });
      window.addEventListener("resize", handleResize);
      rafId = window.requestAnimationFrame(animateScroll);

      return () => {
         if (!scrollContainer) return;
         scrollContainer.removeEventListener("wheel", handleWheel);
         window.removeEventListener("resize", handleResize);
         window.cancelAnimationFrame(rafId);
      };
   });
</script>

<div class="h-screen w-screen overflow-hidden bg-black text-white">
   <div
      bind:this={scrollContainer}
      class="h-full w-full overflow-x-scroll overflow-y-hidden border-y border-[#111] [scrollbar-width:none] [&::-webkit-scrollbar]:hidden"
   >
      <div class="flex h-full w-max">
         {#each sections as section}
            <section
               class="h-full w-screen shrink-0 border-r border-[#1f1f1f] p-8 md:p-12 lg:p-16"
            >
               <header class="mb-8 border-b border-[#111] pb-4">
                  <h2 class="text-2xl uppercase tracking-[0.12em] md:text-3xl">
                     {section.title}
                  </h2>
               </header>

               {#if section.kind === "profile"}
                  <div
                     class="grid h-[calc(100%-4rem)] grid-rows-[auto_1fr] gap-8"
                  >
                     <div class="grid gap-3 border border-[#111] p-6">
                        <p
                           class="text-sm uppercase tracking-[0.1em] text-[#bdbdbd]"
                        >
                           {profile.title}
                        </p>
                        <h1 class="text-4xl uppercase md:text-6xl">
                           {profile.fullName}
                        </h1>
                        <p class="text-base text-[#e0e0e0]">
                           {profile.username}
                        </p>
                     </div>
                     <article
                        class="h-full overflow-y-auto border border-[#111] p-6"
                     >
                        <h3 class="mb-4 text-lg uppercase tracking-[0.08em]">
                           Repository README
                        </h3>
                        <pre
                           class="whitespace-pre-wrap text-sm leading-relaxed text-[#d8d8d8]">{profile.readme}</pre>
                     </article>
                  </div>
               {/if}

               {#if section.kind === "about"}
                  <div
                     class="grid h-[calc(100%-4rem)] grid-cols-1 gap-6 md:grid-cols-2"
                  >
                     <div
                        class="flex h-full flex-col gap-4 border border-[#111] p-6"
                     >
                        {#each aboutColumns.left as paragraph}
                           <p
                              class="text-sm leading-relaxed text-[#e2e2e2] md:text-base"
                           >
                              {paragraph}
                           </p>
                        {/each}
                     </div>
                     <div
                        class="flex h-full flex-col gap-4 border border-[#111] p-6"
                     >
                        {#each aboutColumns.right as paragraph}
                           <p
                              class="text-sm leading-relaxed text-[#e2e2e2] md:text-base"
                           >
                              {paragraph}
                           </p>
                        {/each}
                     </div>
                  </div>
               {/if}

               {#if section.kind === "projects"}
                  <div
                     class="grid h-[calc(100%-4rem)] grid-cols-1 gap-4 md:grid-cols-2"
                  >
                     {#each projects as project}
                        <article
                           class="grid grid-rows-[auto_1fr_auto] gap-4 border border-[#111] p-6"
                        >
                           <h3 class="text-xl uppercase tracking-[0.08em]">
                              {project.name}
                           </h3>
                           <p
                              class="text-sm leading-relaxed text-[#d9d9d9] md:text-base"
                           >
                              {project.description}
                           </p>
                           <p
                              class="text-xs uppercase tracking-[0.08em] text-[#b8b8b8]"
                           >
                              {project.stack.join(" / ")}
                           </p>
                        </article>
                     {/each}
                  </div>
               {/if}

               {#if section.kind === "contact"}
                  <div
                     class="flex h-[calc(100%-4rem)] items-center justify-center"
                  >
                     <div
                        class="w-full max-w-2xl border border-[#111] p-8 text-center"
                     >
                        <h3
                           class="mb-8 text-2xl uppercase tracking-[0.1em] md:text-3xl"
                        >
                           Get in touch
                        </h3>
                        <ul class="space-y-4">
                           {#each contacts as contact}
                              <li
                                 class="border border-[#111] px-4 py-3 text-sm uppercase tracking-[0.08em] text-[#e3e3e3] md:text-base"
                              >
                                 {contact.label}: {contact.value}
                              </li>
                           {/each}
                        </ul>
                     </div>
                  </div>
               {/if}
            </section>
         {/each}
      </div>
   </div>
</div>
