<script lang="ts">
  import { Input } from '$lib/components/ui/input';
  import { Button } from '$lib/components/ui/button';
  import { Label } from '$lib/components/ui/label';

  let selectedDistance = $state('marathon');
  let customDistance = $state('');
  let customUnit = $state('km');
  let goalHours = $state('');
  let goalMinutes = $state('');
  let goalSeconds = $state('');

  let validationError = $state('');
  let calculatedResults = $state<any[]>([]);
  let racePaceSummary = $state('');

  const distancePresets = {
    marathon: { label: 'Marathon', km: 42.195, mi: 26.21875 },
    half_marathon: { label: 'Half Marathon', km: 21.0975, mi: 13.109375 },
    '15k': { label: '15K', km: 15, mi: 9.32057 },
    '10k': { label: '10K', km: 10, mi: 6.21371 },
    '5k': { label: '5K', km: 5, mi: 3.10686 },
    '2mi': { label: '2 Mile', km: 3.21869, mi: 2 },
    '3200m': { label: '3200m', km: 3.2, mi: 1.98838 },
    '3k': { label: '3K', km: 3, mi: 1.86411 },
    '1mi': { label: '1 Mile', km: 1.60934, mi: 1 },
    '1600m': { label: '1600m', km: 1.6, mi: 0.99419 },
    '1500m': { label: '1500m', km: 1.5, mi: 0.93206 }
  };

  function timeToSeconds(hours: string, minutes: string, seconds: string): number {
    return (parseInt(hours) || 0) * 3600 + (parseInt(minutes) || 0) * 60 + (parseInt(seconds) || 0);
  }

  function secondsToTimeString(totalSeconds: number): string {
    const hours = Math.floor(totalSeconds / 3600);
    const minutes = Math.floor((totalSeconds % 3600) / 60);
    const seconds = Math.round(totalSeconds % 60);
    
    return `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
  }

  function getDistanceInKmAndMi(): { km: number; mi: number } | null {
    if (selectedDistance === 'custom') {
      const dist = parseFloat(customDistance);
      if (isNaN(dist) || dist <= 0) {
        return null;
      }
      if (customUnit === 'km') {
        return { km: dist, mi: dist * 0.621371 };
      } else if (customUnit === 'mi') {
        return { km: dist / 0.621371, mi: dist };
      } else {
        return { km: dist / 1000, mi: (dist / 1000) * 0.621371 };
      }
    } else {
      const preset = distancePresets[selectedDistance];
      return { km: preset.km, mi: preset.mi };
    }
  }

  function getCategoryAndColor(percent: number): { category: string; color: string } {
    if (percent >= 115 && percent <= 120) {
      return { category: 'Base Training Speed', color: 'rgb(78, 193, 78)' };
    } else if (percent >= 106 && percent <= 114) {
      return { category: 'Support Training Speed', color: 'rgb(24, 133, 235)' };
    } else if (percent >= 101 && percent <= 105) {
      return { category: 'Specific Training Speed', color: 'rgb(232, 232, 102)' };
    } else if (percent === 100) {
      return { category: 'Race Pace', color: 'rgb(255, 215, 0)' };
    } else if (percent >= 95 && percent <= 99) {
      return { category: 'Specific Training Endurance', color: 'rgb(232, 232, 102)' };
    } else if (percent >= 86 && percent <= 94) {
      return { category: 'Support Training Endurance', color: 'rgb(24, 133, 235)' };
    } else {
      return { category: 'Base Training Endurance', color: 'rgb(78, 193, 78)' };
    }
  }

  function shouldShowCategoryLabel(percent: number, prevPercent: number | null): boolean {
    if (prevPercent === null) return true;
    const current = getCategoryAndColor(percent);
    const previous = getCategoryAndColor(prevPercent);
    return current.category !== previous.category;
  }

  function calculate() {
    validationError = '';
    calculatedResults = [];
    racePaceSummary = '';

    const goalTimeSeconds = timeToSeconds(goalHours, goalMinutes, goalSeconds);
    if (goalTimeSeconds === 0) {
      validationError = 'Please enter a goal time';
      return;
    }

    const distance = getDistanceInKmAndMi();
    if (!distance) {
      validationError = 'Please enter a valid custom distance';
      return;
    }

    const results = [];
    let prevPercent = null;

    for (let percent = 120; percent >= 80; percent--) {
      const adjustedTimeSeconds = goalTimeSeconds / (percent / 100);
      
      const pacePerKmSeconds = adjustedTimeSeconds / distance.km;
      const pacePerMiSeconds = adjustedTimeSeconds / distance.mi;
      
      const pacePerKm = secondsToTimeString(pacePerKmSeconds);
      const pacePerMi = secondsToTimeString(pacePerMiSeconds);
      
      const { category, color } = getCategoryAndColor(percent);
      const showCategory = shouldShowCategoryLabel(percent, prevPercent);
      
      results.push({
        percent,
        pacePerKm,
        pacePerMi,
        category: showCategory ? category : '',
        color
      });
      
      prevPercent = percent;
    }

    calculatedResults = results;

    const racePacePerKmSeconds = goalTimeSeconds / distance.km;
    const racePacePerMiSeconds = goalTimeSeconds / distance.mi;
    const racePacePerKm = secondsToTimeString(racePacePerKmSeconds);
    const racePacePerMi = secondsToTimeString(racePacePerMiSeconds);
    
    racePaceSummary = `To hit your goal time, maintain a pace of ${racePacePerKm} per km (${racePacePerMi} per mile)`;
  }

  function clearAll() {
    selectedDistance = 'marathon';
    customDistance = '';
    customUnit = 'km';
    goalHours = '';
    goalMinutes = '';
    goalSeconds = '';
    validationError = '';
    calculatedResults = [];
    racePaceSummary = '';
  }

  const isCustomDistance = $derived(selectedDistance === 'custom');
</script>

<div class="col-span-1 space-y-2 md:space-y-8">
  <h1 class="scroll-m-20 text-4xl font-extrabold tracking-tight lg:text-5xl">
    Triphasic Training Paces Calculator
  </h1>
  <p class="leading-7 [&:not(:first-child)]:mt-6">
    Calculate training paces based on your goal race time. The percentage bands and formula follow <a
      href="https://www.runelitebook.com/calculator"
      target="_blank"
      class="underline">Andrew Snow / Run Elite's Training Paces Calculator</a
    >.
  </p>

  <div class="space-y-4 rounded-lg bg-white p-6 shadow-lg dark:bg-gray-800">
    <div class="space-y-2">
      <Label for="distance">Distance</Label>
      <select
        id="distance"
        bind:value={selectedDistance}
        class="flex h-10 w-full rounded-md border border-input bg-background px-3 py-2 text-sm ring-offset-background focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2"
      >
        <option value="marathon">Marathon (42.195 km / 26.22 mi)</option>
        <option value="half_marathon">Half Marathon (21.10 km / 13.11 mi)</option>
        <option value="15k">15K (15 km / 9.32 mi)</option>
        <option value="10k">10K (10 km / 6.21 mi)</option>
        <option value="5k">5K (5 km / 3.11 mi)</option>
        <option value="2mi">2 Mile (3.22 km / 2 mi)</option>
        <option value="3200m">3200m (3.2 km / 1.99 mi)</option>
        <option value="3k">3K (3 km / 1.86 mi)</option>
        <option value="1mi">1 Mile (1.61 km / 1 mi)</option>
        <option value="1600m">1600m (1.6 km / 0.99 mi)</option>
        <option value="1500m">1500m (1.5 km / 0.93 mi)</option>
        <option value="custom">Custom Distance</option>
      </select>
    </div>

    {#if isCustomDistance}
      <div class="space-y-2">
        <Label for="customDistance">Custom Distance</Label>
        <div class="grid grid-cols-1 gap-2 sm:grid-cols-2">
          <Input
            id="customDistance"
            type="number"
            placeholder="Enter distance"
            bind:value={customDistance}
            step="0.01"
            min="0"
          />
          <select
            bind:value={customUnit}
            class="flex h-10 w-full rounded-md border border-input bg-background px-3 py-2 text-sm ring-offset-background focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2"
          >
            <option value="km">Kilometers</option>
            <option value="mi">Miles</option>
            <option value="m">Meters</option>
          </select>
        </div>
      </div>
    {/if}

    <div class="space-y-2">
      <Label>Goal Time</Label>
      <div class="grid grid-cols-1 gap-2 sm:grid-cols-3">
        <Input
          type="number"
          placeholder="Hours"
          bind:value={goalHours}
          min="0"
          max="99"
        />
        <Input
          type="number"
          placeholder="Minutes"
          bind:value={goalMinutes}
          min="0"
          max="59"
        />
        <Input
          type="number"
          placeholder="Seconds"
          bind:value={goalSeconds}
          min="0"
          max="59"
        />
      </div>
    </div>

    {#if validationError}
      <p class="text-sm text-red-600 dark:text-red-400">{validationError}</p>
    {/if}

    <div class="grid grid-cols-1 gap-2 sm:grid-cols-2">
      <Button on:click={calculate}>Calculate</Button>
      <Button variant="outline" on:click={clearAll}>Clear All</Button>
    </div>
  </div>
</div>

<div class="col-span-1 space-y-2 md:space-y-8">
  {#if racePaceSummary}
    <div class="rounded-lg bg-white p-6 shadow-lg dark:bg-gray-800">
      <h2 class="scroll-m-20 text-2xl font-extrabold tracking-tight lg:text-3xl mb-4">
        Race Pace Summary
      </h2>
      <p class="text-lg leading-7">{racePaceSummary}</p>
    </div>
  {/if}

  {#if calculatedResults.length > 0}
    <div class="rounded-lg bg-white p-6 shadow-lg dark:bg-gray-800">
      <h2 class="scroll-m-20 text-2xl font-extrabold tracking-tight lg:text-3xl mb-4">
        Training Paces
      </h2>
      <div class="overflow-x-auto">
        <table class="w-full border-collapse">
          <thead>
            <tr class="border-b">
              <th class="p-2 text-left font-semibold">%</th>
              <th class="p-2 text-left font-semibold">Pace/km</th>
              <th class="p-2 text-left font-semibold">Pace/mi</th>
              <th class="p-2 text-left font-semibold">Category</th>
            </tr>
          </thead>
          <tbody>
            {#each calculatedResults as result}
              <tr
                class="border-b hover:bg-gray-50 dark:hover:bg-gray-700"
                style="background-color: {result.color}; opacity: 0.7;"
              >
                <td class="p-2 font-medium">{result.percent}%</td>
                <td class="p-2">{result.pacePerKm}</td>
                <td class="p-2">{result.pacePerMi}</td>
                <td class="p-2 font-semibold">{result.category}</td>
              </tr>
            {/each}
          </tbody>
        </table>
      </div>
    </div>
  {/if}
</div>
