<script lang="ts">
  import { base } from '$app/paths';
  import { Button } from '$lib/components/ui/button';

  const tools = [
    {
      name: 'Pace Calculator',
      description: 'Calculate your pace, time, or distance for running workouts in both miles and kilometers.',
      href: `${base}/pace-calculator`,
      icon: '⏱️'
    },
    {
      name: 'MAF 180 Calculator',
      description: 'Calculate your Maximum Aerobic Function heart rate based on Dr. Phil Maffetone\'s MAF 180 Formula for aerobic training.',
      href: `${base}/maf-180`,
      icon: '❤️'
    },
    {
      name: 'Triphasic Training Paces Calculator',
      description: 'Calculate training paces for Base, Support, and Specific phases based on your goal race time. Inspired by Andrew Snow\'s Run Elite triphasic model.',
      href: `${base}/triphasic-training`,
      icon: '🏃'
    }
  ];
</script>

<div class="col-span-1 md:col-span-2 space-y-8">
  <div class="space-y-4">
    <h1 class="scroll-m-20 text-4xl font-extrabold tracking-tight lg:text-5xl">
      Running & Training Tools
    </h1>
    <p class="text-lg text-muted-foreground">
      A collection of calculators to help you plan and track your running training.
    </p>
  </div>

  <div class="grid gap-6 md:grid-cols-2 lg:grid-cols-3">
    {#each tools as tool}
      <a 
        href={tool.href}
        class="group block rounded-lg border border-border bg-card p-6 shadow-sm transition-all hover:shadow-md hover:border-primary/50"
      >
        <div class="space-y-3">
          <div class="flex items-center gap-3">
            <span class="text-3xl" role="img" aria-hidden="true">{tool.icon}</span>
            <h2 class="text-xl font-semibold tracking-tight group-hover:text-primary transition-colors">
              {tool.name}
            </h2>
          </div>
          <p class="text-sm text-muted-foreground leading-relaxed">
            {tool.description}
          </p>
          <div class="pt-2">
            <Button variant="ghost" size="sm" class="group-hover:bg-primary/10">
              Open Tool →
            </Button>
          </div>
        </div>
      </a>
    {/each}
  </div>

  <div class="rounded-lg border border-border bg-muted/50 p-6 space-y-3">
    <h3 class="text-lg font-semibold">About These Tools</h3>
    <p class="text-sm text-muted-foreground leading-relaxed">
      These calculators are designed to help runners at all levels plan their training more effectively. 
      Whether you're working on pacing strategy, building aerobic base with heart rate training, or 
      following a structured periodization model, these tools provide accurate calculations and 
      research-backed guidance.
    </p>
  </div>
</div>
