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
Calculate training paces based on your goal race time using the triphasic training system. Inspired by Andrew Snow's Run Elite triphasic model ([runelitebook.com](https://www.runelitebook.com/)).

**Route:** `/triphasic-training`

**Features:**
- Distance presets: Marathon, Half Marathon, 15K, 10K, 5K, and more
- Custom distance input (km, miles, or meters)
- Goal time entry (HH:MM:SS)
- Complete pace table from 120% (fastest) to 80% (slowest)
- Filter by training phase: All, Base, Support, Specific
- Color-coded phase distinction:
  - **Base** (120–115% speed / 80–85% endurance) - Green
  - **Support** (114–106% speed / 86–94% endurance) - Blue
  - **Specific** (105–101% speed / 95–99% endurance + 100% race pace) - Amber/Gold
- Both per-kilometer and per-mile paces displayed

**Usage Example:**
1. Select "Marathon" distance
2. Enter goal time: 3 hours, 0 minutes, 0 seconds
3. Click "Calculate"
4. View your race pace (4:16/km or 6:52/mi) and all training paces
5. Filter by phase to focus on specific training zones

**Training Guidance:**
The tool includes practical guidance on how to use the paces in each phase of a race-focused training block. Base establishes the foundation (mostly easy running with strides), Support builds the ladder toward race pace with structured quality work, and Specific assembles race-like sessions.

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
