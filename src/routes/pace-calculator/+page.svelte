<script lang="ts">
  import { Input } from '$lib/components/ui/input';
  import { Button } from '$lib/components/ui/button';
  import { Label } from '$lib/components/ui/label';

  let distance = $state('');
  let timeHours = $state('');
  let timeMinutes = $state('');
  let timeSeconds = $state('');
  let paceMinutes = $state('');
  let paceSeconds = $state('');
  let unit = $state('km'); // or "km"
  let calculationType = $state('pace'); // "time", "pace", or "distance"

  // Store calculated results separately
  let calculatedPaceMinutes = $state('');
  let calculatedPaceSeconds = $state('');
  let calculatedTimeHours = $state('');
  let calculatedTimeMinutes = $state('');
  let calculatedTimeSeconds = $state('');
  let calculatedDistance = $state('');

  // Validation state
  let distanceError = $state('');
  let timeHoursError = $state('');
  let timeMinutesError = $state('');
  let timeSecondsError = $state('');
  let paceMinutesError = $state('');
  let paceSecondsError = $state('');
  let hasErrors = $state(false);

  // Convert time to total seconds
  function timeToSeconds(hours, minutes, seconds) {
    return (parseInt(hours) || 0) * 3600 + (parseInt(minutes) || 0) * 60 + (parseInt(seconds) || 0);
  }

  // Convert seconds to time object
  function secondsToTime(totalSeconds) {
    const hours = Math.floor(totalSeconds / 3600);
    const minutes = Math.floor((totalSeconds % 3600) / 60);
    const seconds = totalSeconds % 60;
    return { hours, minutes, seconds };
  }

  // Calculate pace from distance and time
  function calculatePace() {
    if (!distance || (!timeHours && !timeMinutes && !timeSeconds)) return;

    const totalTime = timeToSeconds(timeHours, timeMinutes, timeSeconds);
    const distanceNum = parseFloat(distance);

    if (totalTime === 0 || distanceNum === 0) return;

    const paceSeconds = totalTime / distanceNum;
    const paceTime = secondsToTime(Math.round(paceSeconds));

    calculatedPaceMinutes = paceTime.minutes.toString();
    calculatedPaceSeconds = paceTime.seconds.toString().padStart(2, '0');
  }

  // Calculate time from distance and pace
  function calculateTime() {
    if (!distance || (!paceMinutes && !paceSeconds)) return;

    const paceTotalSeconds = timeToSeconds('0', paceMinutes, paceSeconds);
    const distanceNum = parseFloat(distance);

    if (paceTotalSeconds === 0 || distanceNum === 0) return;

    const totalTime = paceTotalSeconds * distanceNum;
    const time = secondsToTime(Math.round(totalTime));

    calculatedTimeHours = time.hours.toString();
    calculatedTimeMinutes = time.minutes.toString();
    calculatedTimeSeconds = time.seconds.toString().padStart(2, '0');
  }

  // Calculate distance from time and pace
  function calculateDistance() {
    if ((!timeHours && !timeMinutes && !timeSeconds) || (!paceMinutes && !paceSeconds)) return;

    const totalTime = timeToSeconds(timeHours, timeMinutes, timeSeconds);
    const paceTotalSeconds = timeToSeconds('0', paceMinutes, paceSeconds);

    if (totalTime === 0 || paceTotalSeconds === 0) return;

    calculatedDistance = (totalTime / paceTotalSeconds).toFixed(2);
  }

  // Handle calculation based on type
  function handleCalculate() {
    // Set interaction flag when user tries to calculate
    hasInteracted = true;

    // Validate all fields before calculating
    if (!validateAllFields()) {
      return; // Stop if validation fails
    }

    if (calculationType === 'pace') {
      calculatePace();
    } else if (calculationType === 'time') {
      calculateTime();
    } else if (calculationType === 'distance') {
      calculateDistance();
    }

    // Scroll to results after a short delay to ensure DOM is updated
    setTimeout(() => {
      const resultsElement = document.getElementById('results-section');
      if (resultsElement) {
        resultsElement.scrollIntoView({
          behavior: 'smooth',
          block: 'start'
        });
      }
    }, 100);
  }

  // Validation functions
  function validateDistance() {
    if (!distance) {
      distanceError = 'Distance is required';
      return false;
    }
    const distanceNum = parseFloat(distance);
    if (isNaN(distanceNum) || distanceNum <= 0) {
      distanceError = 'Distance must be a positive number';
      return false;
    }
    if (distanceNum > 1000) {
      distanceError = 'Distance seems too large (max 1000)';
      return false;
    }
    distanceError = '';
    return true;
  }

  function validateTimeHours() {
    if (!timeHours) {
      timeHoursError = '';
      return true; // Hours can be empty
    }
    const hours = parseInt(timeHours);
    if (isNaN(hours) || hours < 0) {
      timeHoursError = 'Hours must be a positive number';
      return false;
    }
    if (hours > 99) {
      timeHoursError = 'Hours cannot exceed 99';
      return false;
    }
    timeHoursError = '';
    return true;
  }

  function validateTimeMinutes() {
    if (!timeMinutes) {
      timeMinutesError = '';
      return true; // Minutes can be empty
    }
    const minutes = parseInt(timeMinutes);
    if (isNaN(minutes) || minutes < 0) {
      timeMinutesError = 'Minutes must be a positive number';
      return false;
    }
    if (minutes > 59) {
      timeMinutesError = 'Minutes cannot exceed 59';
      return false;
    }
    timeMinutesError = '';
    return true;
  }

  function validateTimeSeconds() {
    if (!timeSeconds) {
      timeSecondsError = '';
      return true; // Seconds can be empty
    }
    const seconds = parseInt(timeSeconds);
    if (isNaN(seconds) || seconds < 0) {
      timeSecondsError = 'Seconds must be a positive number';
      return false;
    }
    if (seconds > 59) {
      timeSecondsError = 'Seconds cannot exceed 59';
      return false;
    }
    timeSecondsError = '';
    return true;
  }

  function validatePaceMinutes() {
    if (!paceMinutes) {
      paceMinutesError = '';
      return true; // Minutes can be empty
    }
    const minutes = parseInt(paceMinutes);
    if (isNaN(minutes) || minutes < 0) {
      paceMinutesError = 'Pace minutes must be a positive number';
      return false;
    }
    if (minutes > 59) {
      paceMinutesError = 'Pace minutes cannot exceed 59';
      return false;
    }
    paceMinutesError = '';
    return true;
  }

  function validatePaceSeconds() {
    if (!paceSeconds) {
      paceSecondsError = '';
      return true; // Seconds can be empty
    }
    const seconds = parseInt(paceSeconds);
    if (isNaN(seconds) || seconds < 0) {
      paceSecondsError = 'Pace seconds must be a positive number';
      return false;
    }
    if (seconds > 59) {
      paceSecondsError = 'Pace seconds cannot exceed 59';
      return false;
    }
    paceSecondsError = '';
    return true;
  }

  function validateAllFields() {
    const validDistance = validateDistance();
    const validTimeHours = validateTimeHours();
    const validTimeMinutes = validateTimeMinutes();
    const validTimeSeconds = validateTimeSeconds();
    const validPaceMinutes = validatePaceMinutes();
    const validPaceSeconds = validatePaceSeconds();

    // Check if at least one time field is filled when needed
    let validTimeFields = true;
    if (calculationType === 'pace' || calculationType === 'distance') {
      if (!timeHours && !timeMinutes && !timeSeconds) {
        timeHoursError = 'At least one time field is required';
        validTimeFields = false;
      } else {
        // Clear time error if fields are now filled
        if (timeHoursError === 'At least one time field is required') {
          timeHoursError = '';
        }
      }
    } else {
      // Clear time errors when time fields are not required
      if (timeHoursError === 'At least one time field is required') {
        timeHoursError = '';
      }
    }

    // Check if at least one pace field is filled when needed
    let validPaceFields = true;
    if (calculationType === 'time' || calculationType === 'distance') {
      if (!paceMinutes && !paceSeconds) {
        paceMinutesError = 'At least one pace field is required';
        validPaceFields = false;
      } else {
        // Clear pace error if fields are now filled
        if (paceMinutesError === 'At least one pace field is required') {
          paceMinutesError = '';
        }
      }
    } else {
      // Clear pace errors when pace fields are not required
      if (paceMinutesError === 'At least one pace field is required') {
        paceMinutesError = '';
      }
    }

    hasErrors = !(
      validDistance &&
      validTimeHours &&
      validTimeMinutes &&
      validTimeSeconds &&
      validPaceMinutes &&
      validPaceSeconds &&
      validTimeFields &&
      validPaceFields
    );
    return !hasErrors;
  }

  // Clear all inputs
  function clearAll() {
    // Clear all input values
    distance = '';
    timeHours = '';
    timeMinutes = '';
    timeSeconds = '';
    paceMinutes = '';
    paceSeconds = '';

    // Clear calculated results
    calculatedPaceMinutes = '';
    calculatedPaceSeconds = '';
    calculatedTimeHours = '';
    calculatedTimeMinutes = '';
    calculatedTimeSeconds = '';
    calculatedDistance = '';

    // Clear all validation errors immediately
    distanceError = '';
    timeHoursError = '';
    timeMinutesError = '';
    timeSecondsError = '';
    paceMinutesError = '';
    paceSecondsError = '';
    hasErrors = false;

    // Reset interaction flag
    hasInteracted = false;
  }

  // Format calculated results for display
  const formattedCalculatedPace = $derived(
    calculatedPaceMinutes && calculatedPaceSeconds
      ? `${calculatedPaceMinutes}:${calculatedPaceSeconds}`
      : ''
  );

  const formattedCalculatedTime = $derived(
    calculatedTimeHours || calculatedTimeMinutes || calculatedTimeSeconds
      ? `${calculatedTimeHours || '0'}:${(calculatedTimeMinutes || '0').padStart(2, '0')}:${(calculatedTimeSeconds || '0').padStart(2, '0')}`
      : ''
  );

  // Format input values for display
  const formattedInputPace = $derived(
    paceMinutes && paceSeconds ? `${paceMinutes}:${paceSeconds}` : ''
  );

  const formattedInputTime = $derived(
    timeHours || timeMinutes || timeSeconds
      ? `${timeHours || '0'}:${(timeMinutes || '0').padStart(2, '0')}:${(timeSeconds || '0').padStart(2, '0')}`
      : ''
  );

  // Track if user has interacted with the form
  let hasInteracted = $state(false);

  // Reactive validation - update hasErrors whenever any field changes
  $effect(() => {
    if (
      hasInteracted &&
      (distance || timeHours || timeMinutes || timeSeconds || paceMinutes || paceSeconds)
    ) {
      validateAllFields();
    }
  });

  // Reactive validation when calculation type changes
  $effect(() => {
    if (hasInteracted) {
      validateAllFields();
    }
  });
</script>

<div class="col-span-1 space-y-2 md:space-y-8">
  <!-- Header -->
  <h1 class="scroll-m-20 text-4xl font-extrabold tracking-tight lg:text-5xl">Pace Calculator</h1>
  <p class="leading-7 [&:not(:first-child)]:mt-6">
    Calculate your pace, time or distance in both miles and kilometers
  </p>

  <!-- Unit Toggle -->
  <div class="flex justify-center space-x-2">
    <Button variant={unit === 'km' ? 'default' : 'outline'} on:click={() => (unit = 'km')}>
      Kilometers
    </Button>
    <Button variant={unit === 'miles' ? 'default' : 'outline'} on:click={() => (unit = 'miles')}>
      Miles
    </Button>
  </div>

  <!-- Calculation Type Toggle -->
  <div class="grid grid-cols-1 gap-2 sm:grid-cols-3 sm:gap-4">
    <Button
      variant={calculationType === 'pace' ? 'default' : 'outline'}
      on:click={() => (calculationType = 'pace')}
    >
      Calculate Pace
    </Button>
    <Button
      variant={calculationType === 'time' ? 'default' : 'outline'}
      on:click={() => (calculationType = 'time')}
    >
      Calculate Time
    </Button>
    <Button
      variant={calculationType === 'distance' ? 'default' : 'outline'}
      on:click={() => (calculationType = 'distance')}
    >
      Calculate Distance
    </Button>
  </div>

  <!-- Calculator Form -->
  <div class="space-y-2 rounded-lg bg-white p-6 shadow-lg dark:bg-gray-800">
    <!-- Distance Input - Show for Pace and Time calculations -->
    {#if calculationType === 'pace' || calculationType === 'time'}
      <div class="space-y-2">
        <Label for="distance">Distance ({unit})</Label>
        <Input
          id="distance"
          type="number"
          placeholder="Enter distance"
          bind:value={distance}
          step="0.01"
          min="0"
          class={distanceError ? 'border-red-500 focus-visible:ring-red-500' : ''}
          on:input={() => {
            if (distanceError) validateDistance();
          }}
        />
        {#if distanceError}
          <p class="text-sm text-red-600 dark:text-red-400">{distanceError}</p>
        {/if}
      </div>
    {/if}

    <!-- Time Input - Show for Pace and Distance calculations -->
    {#if calculationType === 'pace' || calculationType === 'distance'}
      <div class="space-y-2">
        <Label>Time</Label>
        <div class="grid grid-cols-1 gap-2 sm:grid-cols-3">
          <div>
            <Input
              type="number"
              placeholder="Hours"
              bind:value={timeHours}
              min="0"
              max="99"
              class={timeHoursError ? 'border-red-500 focus-visible:ring-red-500' : ''}
              on:input={() => {
                if (timeHoursError) validateTimeHours();
              }}
            />
          </div>
          <div>
            <Input
              type="number"
              placeholder="Minutes"
              bind:value={timeMinutes}
              min="0"
              max="59"
              class={timeMinutesError ? 'border-red-500 focus-visible:ring-red-500' : ''}
              on:input={() => {
                if (timeMinutesError) validateTimeMinutes();
              }}
            />
          </div>
          <div>
            <Input
              type="number"
              placeholder="Seconds"
              bind:value={timeSeconds}
              min="0"
              max="59"
              class={timeSecondsError ? 'border-red-500 focus-visible:ring-red-500' : ''}
              on:input={() => {
                if (timeSecondsError) validateTimeSeconds();
              }}
            />
          </div>
        </div>
        {#if timeHoursError || timeMinutesError || timeSecondsError}
          <div class="space-y-1">
            {#if timeHoursError}
              <p class="text-sm text-red-600 dark:text-red-400">{timeHoursError}</p>
            {/if}
            {#if timeMinutesError}
              <p class="text-sm text-red-600 dark:text-red-400">{timeMinutesError}</p>
            {/if}
            {#if timeSecondsError}
              <p class="text-sm text-red-600 dark:text-red-400">{timeSecondsError}</p>
            {/if}
          </div>
        {/if}
      </div>
    {/if}

    <!-- Pace Input - Show for Time and Distance calculations -->
    {#if calculationType === 'time' || calculationType === 'distance'}
      <div class="space-y-2">
        <Label>Pace (per {unit})</Label>
        <div class="grid grid-cols-1 gap-2 sm:grid-cols-2">
          <div>
            <Input
              type="number"
              placeholder="Minutes"
              bind:value={paceMinutes}
              min="0"
              max="59"
              class={paceMinutesError ? 'border-red-500 focus-visible:ring-red-500' : ''}
              on:input={() => {
                if (paceMinutesError) validatePaceMinutes();
              }}
            />
          </div>
          <div>
            <Input
              type="number"
              placeholder="Seconds"
              bind:value={paceSeconds}
              min="0"
              max="59"
              class={paceSecondsError ? 'border-red-500 focus-visible:ring-red-500' : ''}
              on:input={() => {
                if (paceSecondsError) validatePaceSeconds();
              }}
            />
          </div>
        </div>
        {#if paceMinutesError || paceSecondsError}
          <div class="space-y-1">
            {#if paceMinutesError}
              <p class="text-sm text-red-600 dark:text-red-400">{paceMinutesError}</p>
            {/if}
            {#if paceSecondsError}
              <p class="text-sm text-red-600 dark:text-red-400">{paceSecondsError}</p>
            {/if}
          </div>
        {/if}
      </div>
    {/if}

    <!-- Action Buttons -->
    <div class="grid grid-cols-1 gap-2 sm:grid-cols-2">
      <Button on:click={handleCalculate} disabled={hasErrors}>Calculate</Button>
      <Button variant="outline" on:click={clearAll}>Clear All</Button>
    </div>
  </div>
</div>
<div class="col-span-1 space-y-2 md:space-y-8">
  <!-- Results Display -->
  <div id="results-section" class="max-w-l text-xxl flex w-full flex-col gap-1.5">
    <h1 class="scroll-m-20 text-2xl font-extrabold tracking-tight lg:text-3xl">Results</h1>
    {#if (calculationType === 'pace' && formattedCalculatedPace) || (calculationType === 'time' && formattedCalculatedTime) || (calculationType === 'distance' && calculatedDistance)}
      {#if calculationType === 'pace' && formattedCalculatedPace}
        <h1 class="scroll-m-20 text-2xl font-light tracking-tight lg:text-3xl">Your pace</h1>
        <h1 class="scroll-m-20 text-4xl font-bold tracking-tight lg:text-5xl">
          {formattedCalculatedPace} per {unit}
        </h1>
      {/if}

      {#if calculationType === 'time' && formattedCalculatedTime}
        <h1 class="scroll-m-20 text-2xl font-light tracking-tight lg:text-3xl">Your time</h1>
        <h1 class="scroll-m-20 text-4xl font-bold tracking-tight lg:text-5xl">
          {formattedCalculatedTime} per {unit}
        </h1>
      {/if}

      {#if calculationType === 'distance' && calculatedDistance}
        <h1 class="scroll-m-20 text-2xl font-light tracking-tight lg:text-3xl">Your distance</h1>
        <h1 class="scroll-m-20 text-4xl font-bold tracking-tight lg:text-5xl">
          {calculatedDistance}
          {unit}
        </h1>
      {/if}
    {/if}
  </div>
</div>
