<script lang="ts">
  import '../app.css';
  let { children } = $props();
  import CircleUser from 'lucide-svelte/icons/circle-user';
  import Menu from 'lucide-svelte/icons/menu';
  import Calculator from 'lucide-svelte/icons/calculator';
  import Search from 'lucide-svelte/icons/search';

  import { Button } from '$lib/components/ui/button/index.js';
  import * as DropdownMenu from '$lib/components/ui/dropdown-menu/index.js';
  import { Input } from '$lib/components/ui/input/index.js';
  import * as Sheet from '$lib/components/ui/sheet/index.js';
  import { base } from '$app/paths'; // Import the base path
</script>

<div class="flex min-h-screen w-full flex-col">
  <header class="sticky top-0 flex h-16 items-center gap-4 border-b bg-background px-4 md:px-6">
    <nav
      class="hidden flex-col gap-6 text-lg font-medium md:flex md:flex-row md:items-center md:gap-5 md:text-sm lg:gap-6"
    >
      <a href="{base}/" class="flex items-center gap-2 text-lg font-semibold md:text-base">
        <Calculator class="h-6 w-6" />
        <span class="sr-only">Tools</span>
      </a>
      <a
        href="{base}/pace-calculator"
        class="text-foreground transition-colors hover:text-foreground"
      >
        Pace Calculator
      </a>
      <a href="{base}/maf-180" class="text-foreground transition-colors hover:text-foreground">
        MAF 180
      </a>
    </nav>
    <Sheet.Root>
      <Sheet.Trigger asChild let:builder>
        <Button variant="outline" size="icon" class="shrink-0 md:hidden" builders={[builder]}>
          <Menu class="h-5 w-5" />
          <span class="sr-only">Toggle navigation menu</span>
        </Button>
      </Sheet.Trigger>
      <Sheet.Content side="left">
        <nav class="grid gap-6 text-lg font-medium">
          <a href="{base}/" class="flex items-center gap-2 text-lg font-semibold">
            <Calculator class="h-6 w-6" />
            <span class="sr-only">Tools</span>
          </a>
          <a href="{base}/maf-180" class="hover:text-foreground"> MAF 180 </a>
        </nav>
      </Sheet.Content>
    </Sheet.Root>
  </header>
  <main class="flex flex-1 flex-col gap-4 p-4 md:gap-8 md:p-8">
    <div class="grid gap-4 md:grid-cols-1 md:gap-8 lg:grid-cols-2">
      {@render children()}
    </div>
  </main>
</div>
