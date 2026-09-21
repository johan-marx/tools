# Running & Training Tools

A collection of web-based calculators for runners, built with SvelteKit and Tailwind CSS.

## Available Tools

### Pace Calculator
Calculate your pace, time, or distance for running workouts in both miles and kilometers.

**Route:** `/pace-calculator`

### MAF 180 Calculator
Calculate your Maximum Aerobic Function heart rate based on the MAF 180 Formula developed by Dr. Phil Maffetone.

**Route:** `/maf-180`

### Triphasic Training Paces Calculator
Calculate training paces based on your goal race time using the triphasic training system. The percentage bands and formula follow [Andrew Snow / Run Elite's Training Paces Calculator](https://www.runelitebook.com/calculator).

**Route:** `/triphasic-training`

**Features:**
- Distance presets: Marathon, Half Marathon, 15K, 10K, 5K, and more
- Custom distance input (km, miles, or meters)
- Goal time entry (HH:MM:SS)
- Complete pace table from 120% (fastest) to 80% (slowest)
- Color-coded training phases:
  - **Base Training Speed** (120%-115%) - Green
  - **Support Training Speed** (114%-106%) - Blue
  - **Specific Training Speed** (105%-101%) - Yellow
  - **Race Pace** (100%) - Gold
  - **Specific Training Endurance** (99%-95%) - Yellow
  - **Support Training Endurance** (94%-86%) - Blue
  - **Base Training Endurance** (85%-80%) - Green
- Both per-kilometer and per-mile paces displayed

**Usage Example:**
1. Select "Marathon" distance
2. Enter goal time: 3 hours, 0 minutes, 0 seconds
3. Click "Calculate"
4. View your race pace (4:16/km or 6:52/mi) and all training paces

## Developing

Once you've created a project and installed dependencies with `npm install` (or `pnpm install` or `yarn`), start a development server:

```bash
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

## Building

To create a production version of your app:

```bash
npm run build
```

You can preview the production build with `npm run preview`.

> To deploy your app, you may need to install an [adapter](https://svelte.dev/docs/kit/adapters) for your target environment.
