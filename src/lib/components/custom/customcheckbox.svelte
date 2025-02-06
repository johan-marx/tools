<script lang="ts">
  import { Label } from '$lib/components/ui/label/index.js';
  import { Checkbox } from '$lib/components/ui/checkbox';

  let { checked = $bindable(), disabled = $bindable() } = $props();
</script>

<div class="flex items-center space-x-2">
  <Checkbox bind:disabled bind:checked />
  <Label
    class="text-sm font-medium leading-none peer-disabled:cursor-not-allowed peer-disabled:opacity-70"
  >
    <slot />
  </Label>
</div>
