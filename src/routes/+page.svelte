<script lang="ts">
  import { base } from '$app/paths';

  const tools = [
    {
      name: 'Pace Calculator',
      description: 'Calculate pace, time, or distance.',
      href: `${base}/pace-calculator`
    },
    {
      name: 'MAF 180',
      description: 'Calculate Maximum Aerobic Function heart rate.',
      href: `${base}/maf-180`
    },
    {
      name: 'Triphasic Training',
      description: 'Calculate training paces for Base, Support, and Specific phases.',
      href: `${base}/triphasic-training`
    }
  ];
</script>

<div class="col-span-1 md:col-span-2">
  <h1 class="scroll-m-20 text-2xl font-semibold tracking-tight mb-6">Tools</h1>
  
  <div class="space-y-1">
    {#each tools as tool}
      <a 
        href={tool.href}
        class="block py-3 px-4 -mx-4 rounded hover:bg-muted transition-colors"
      >
        <div class="font-medium">{tool.name}</div>
        <div class="text-sm text-muted-foreground">{tool.description}</div>
      </a>
    {/each}
  </div>
</div>
