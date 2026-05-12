# Project Overview — OPTCG Card Animations

## Vision
Create interactive, visually stunning web prototypes that bring One Piece Trading Card Game cards to life with animation effects. These experiments explore what's possible with browser-native tech (CSS 3D, JS, device APIs) before deciding what to productionize.

## The Three Experiments

### Experiment 1: 3D Card Tilt & Flip
**Foundation layer.** A single card that responds to mouse movement (desktop) and device tilt (mobile) with perspective-based 3D transforms. Includes click-to-flip revealing a styled card back. This establishes the core card component that Experiments 2 and 3 build on.

### Experiment 2: Holographic Foil Shimmer
**Visual layer.** A CSS gradient overlay on top of the tilt card that shifts based on mouse/tilt position, simulating the rainbow light refraction of a real holographic foil card. Reacts to the same input data as the tilt. Applied selectively — common cards get no shimmer, rares get full holo treatment.

### Experiment 3: Pack Opening Simulation
**Experience layer.** An animated sequence: tap a booster pack, it tears open, and cards fan out or slide into view one at a time. Each revealed card uses the tilt + holo effects from Experiments 1 and 2. The rare card gets extra emphasis (sparkle particles, dramatic pause).

## Build Order
Experiments are built sequentially. Each one layers on the previous:
```
Exp 1 (Tilt + Flip) → Exp 2 (Holo Shimmer) → Exp 3 (Pack Opening)
```

## Workflow

### Roles
- **This chat (Claude.ai):** Planning, experiment design, writing coding prompts, reviewing results
- **Claude Code:** Writing and iterating on the actual code
- **Pencil:** Any design work (layouts, visual mockups, asset prep)

### Coding Process
1. We plan the experiment and write a detailed step-by-step build plan here
2. The build plan is saved to `docs/EXPERIMENT-{N}-PLAN.md`
3. A kickoff prompt is written in `docs/CC-PROMPT.md`
4. Claude Code works through the plan ONE STEP AT A TIME
5. After each step, CC stops and shows results
6. User reviews and approves before CC proceeds
7. Repeat until the experiment is complete

### Key Rule
**Claude Code must STOP after each step and wait for approval.** No skipping ahead, no bundling steps. Each checkpoint is a gate.

## Target Platform
- Web (HTML/CSS/JS)
- Must work on desktop (mouse interaction) and mobile (touch + gyroscope)
- Single HTML file per experiment (no build tools)
- Modern browsers: Chrome, Safari, Firefox

## Asset Sources
Card images and data come from the OPTCG API. See `ASSET-REFERENCE.md` for full details.
Primary reference site: https://en.onepiece-cardgame.com
