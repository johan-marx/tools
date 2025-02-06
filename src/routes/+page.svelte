<script lang="ts">
  import { Button } from '$lib/components/ui/button';
  import { Input } from '$lib/components/ui/input/index.js';
  import { Label } from '$lib/components/ui/label/index.js';
  import { Checkbox } from '$lib/components/ui/checkbox';
  import CustomCheckbox from '$lib/components/custom/customcheckbox.svelte';
  //   let min10 = [false, false, false, false];
  let age = $state(25);
  let min10a = $state(false);
  let min10b = $state(false);
  let min10c = $state(false);
  let min10d = $state(false);
  let min5a = $state(false);
  let min5b = $state(false);
  let min5c = $state(false);
  let min5d = $state(false);
  let min5e = $state(false);
  let min5f = $state(false);
  let min5g = $state(false);

  let plus0a = $state(false);
  let plus5a = $state(false);
  let plus5b = $state(false);
  let plus5c = $state(false);
  let plus5d = $state(false);

  let baseHr = $derived.by(() => {
    if (age && age <= 16) {
      return 165;
    }
    return 180;
  });

  let min10 = $derived(min10a || min10b || min10c || min10d);
  let min5 = $derived(min5a || min5b || min5c || min5d || min5e || min5f || min5g);
  let plus5 = $derived(plus5a || plus5b || plus5c || plus5d);
  let plus0 = $derived(plus0a);

  $effect(() => {
    if (min10 || min5) {
      plus0a = plus5a = plus5b = plus5c = plus5d = false;
    }
    if (plus0) {
      plus5a = plus5b = plus5c = plus5d = false;
    }
    if (plus5) {
      plus0a = false;
    }
  });

  let hr = $derived.by(() => {
    return baseHr - age - min10 * 10 - min5 * 5 + plus5 * 5;
  });
</script>

<div class="max-w-l flex w-full flex-col gap-1.5">
  <h1 class="scroll-m-20 text-4xl font-extrabold tracking-tight lg:text-5xl">MAF 180 Calculator</h1>
  <p class="leading-7 [&:not(:first-child)]:mt-6">
    The MAF 180 Formula (Maximum Aerobic Function) is a heart rate training method developed by Dr.
    Phil Maffetone to enhance aerobic endurance and overall health. Read more <a
      class="underline"
      href="https://philmaffetone.com/180-formula/"
      target="_blank"
      >here.
    </a>
  </p>
  <Label for="age">Age</Label>
  <Input bind:value={age} type="number" min="0" max="100" id="age" placeholder="Age" />

  <Label class="mt-3 text-muted-foreground">-10 Modifiers</Label>
  <CustomCheckbox bind:checked={min10a}>I have or am recovering from a major illness</CustomCheckbox
  >
  <CustomCheckbox bind:checked={min10b}>I am in rehabilitation</CustomCheckbox>
  <CustomCheckbox bind:checked={min10c}>I am on regular medication</CustomCheckbox>
  <CustomCheckbox bind:checked={min10d}
    >I am in <a
      class="underline"
      href="https://philmaffetone.com/the-overtraining-syndrome/"
      target="_blank"
      >stage 3 overtraining
    </a></CustomCheckbox
  >

  <Label class="mt-3 text-muted-foreground">-5 Modifiers</Label>
  <CustomCheckbox bind:checked={min5a}>I am injured</CustomCheckbox>
  <CustomCheckbox bind:checked={min5b}
    >I have regressed or not improved in training or competition</CustomCheckbox
  >
  <CustomCheckbox bind:checked={min5c}
    >I get more than two colds, flu or other infections per year</CustomCheckbox
  >
  <CustomCheckbox bind:checked={min5d}>I have seasonal allergies or asthma</CustomCheckbox>
  <CustomCheckbox bind:checked={min5e}
    >I am <a
      class="underline"
      href="https://www.health.harvard.edu/heart-health/overweight-vs-overfat-is-your-scale-lying-to-you"
      target="_blank"
      >overfat
    </a></CustomCheckbox
  >
  <CustomCheckbox bind:checked={min5f}
    >I am in <a
      class="underline"
      href="https://philmaffetone.com/the-overtraining-syndrome/"
      target="_blank"
      >stage 1 or 2 of overtraining
    </a></CustomCheckbox
  >
  <CustomCheckbox bind:checked={min5g}
    >I have been inconsistent, just starting, or just getting back into training</CustomCheckbox
  >

  <Label class="mt-3 text-muted-foreground">+0 Modifier</Label>

  <CustomCheckbox disabled={min5 || min10 || plus5} bind:checked={plus0a}>
    I have been consistantly training (4+ times a week) for <span class="font-bold"
      >less than two years
    </span> without any of the problems listed above</CustomCheckbox
  >
  <Label class="mt-3 text-muted-foreground">+5 Modifiers</Label>
  <Label
    >I have been consistantly training (4+ times a week) for <span class="font-bold">
      more than two years</span
    > and:</Label
  >
  <CustomCheckbox disabled={min5 || min10 || plus0} bind:checked={plus5a}
    >I haven't experienced any of the problems listed above</CustomCheckbox
  >
  <CustomCheckbox disabled={min5 || min10 || plus0} bind:checked={plus5b}
    >I have made progress in my training</CustomCheckbox
  >
  <CustomCheckbox disabled={min5 || min10 || plus0} bind:checked={plus5c}
    >I have improved competitively</CustomCheckbox
  >
  <CustomCheckbox disabled={min5 || min10 || plus0} bind:checked={plus5d}
    >I am without injury</CustomCheckbox
  >
</div>

<div class="max-w-l text-xxl flex w-full flex-col gap-1.5">
  <h1 class="scroll-m-20 text-2xl font-extrabold tracking-tight lg:text-3xl">
    Maximum Aerobic Heart Rate
  </h1>
  <h1 class="scroll-m-20 text-2xl font-light tracking-tight lg:text-3xl">{baseHr}</h1>

  {#if age}
    <h1 class="scroll-m-20 text-2xl font-light tracking-tight lg:text-3xl">-{age}</h1>
  {/if}

  {#if min10}
    <h1 class="scroll-m-20 text-2xl font-light tracking-tight lg:text-3xl">-10</h1>
  {/if}

  {#if min5}
    <h1 class="scroll-m-20 text-2xl font-light tracking-tight lg:text-3xl">-5</h1>
  {/if}

  {#if plus0 && !plus5}
    <h1 class="scroll-m-20 text-2xl font-light tracking-tight lg:text-3xl">+0</h1>
  {/if}

  {#if plus5}
    <h1 class="scroll-m-20 text-2xl font-light tracking-tight lg:text-3xl">+5</h1>
  {/if}
  <h1 class="scroll-m-20 text-4xl font-bold tracking-tight lg:text-5xl">{hr}</h1>
</div>
