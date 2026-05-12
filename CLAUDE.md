# OPTCG Card Animations Project

## What This Is
A series of web-based animation experiments for One Piece Trading Card Game (OPTCG) cards. Each experiment is a standalone HTML/CSS/JS prototype exploring different visual effects.

## Project Structure
```
/Users/cdevine/Developer/OPcard-experiments/
├── CLAUDE.md              ← You are here. Read this first.
├── docs/
│   ├── PROJECT-OVERVIEW.md   ← Big picture, workflow, goals
│   ├── ASSET-REFERENCE.md    ← API details, image URLs, card IDs
│   ├── EXPERIMENT-1-PLAN.md  ← Step-by-step build plan for Experiment 1
│   ├── EXPERIMENT-2-PLAN.md  ← Step-by-step build plan for Experiment 2
│   ├── EXPERIMENT-2B-PLAN.md ← Adjustment: set browsing + variant tray
│   ├── EXPERIMENT-3-PLAN.md  ← Step-by-step build plan for Experiment 3
│   └── CC-PROMPT.md          ← Start here when beginning work
└── experiments/
    ├── exp1/                 ← Card viewer (Experiments 1, 2, 2B)
    │   └── index.html
    └── exp3/                 ← Pack opening simulation (Experiment 3)
        └── index.html
```

## Critical Workflow Rules

### 1. STOP AFTER EVERY STEP
Each experiment plan is broken into numbered steps with checkpoints. You MUST:
- Complete only ONE step at a time
- STOP after each step and show the result
- Wait for explicit user approval before proceeding to the next step
- Do NOT bundle steps together or skip ahead

### 2. Read the Docs First
Before starting any work:
1. Read `docs/CC-PROMPT.md` for the current task
2. Read `docs/PROJECT-OVERVIEW.md` for context
3. Read `docs/ASSET-REFERENCE.md` for API/image details
4. Read the relevant experiment plan (e.g. `docs/EXPERIMENT-1-PLAN.md`)

### 3. Single-File Prototypes
Each experiment produces a single HTML file with embedded CSS and JS. No build tools, no frameworks, no external dependencies (unless specifically noted). Vanilla JS only.

### 4. Asset Sources
- Primary: OPTCG API at `https://optcgapi.com/` for card images and data
- Reference: `https://en.onepiece-cardgame.com` for official card info
- See `docs/ASSET-REFERENCE.md` for full details

## Experiments

| # | Name | Status | Description |
|---|------|--------|-------------|
| 1 | 3D Card Tilt & Flip | ✅ Complete | Interactive 3D tilt, flip animation, mobile gyroscope |
| 2 | Holographic Foil Shimmer | ✅ Complete | Rainbow gradient overlay + line texture reacting to mouse/tilt |
| 2B | Set Browsing & Variant Tray | ✅ Complete | Arrows browse sets, long-press for variant tray |
| 3 | Pack Opening Simulation | 🟡 In Progress | Animated booster pack tear → card reveal sequence |

## Tech Stack
- HTML / CSS / JS (vanilla, single-file)
- No frameworks or build tools
- Must work on both desktop (mouse) and mobile (touch + gyroscope)
- Target: modern browsers (Chrome, Safari, Firefox)
