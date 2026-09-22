# Running & Training Tools

Running calculators built with SvelteKit.

## Tools

Visit `/` for an index of available tools.

### Pace Calculator
Calculate pace, time, or distance.

**Route:** `/pace-calculator`

### MAF 180
Calculate Maximum Aerobic Function heart rate.

**Route:** `/maf-180`

### Triphasic Training
Calculate training paces for Base, Support, and Specific phases. Based on Andrew Snow's Run Elite triphasic model ([runelitebook.com](https://www.runelitebook.com/)).

**Route:** `/triphasic-training`

**Features:**
- Distance presets (Marathon, Half Marathon, 15K, 10K, 5K, etc.) or custom distance
- Goal time input (HH:MM:SS)
- Pace table from 120% down to 80%
- Filter by phase: All, Base, Support, Specific
- Displays per-km and per-mi paces
- Includes training guidance for each phase

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
