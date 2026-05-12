# Current Task: Experiment 2B — Set Browsing & Variant Tray

## Before You Start

Read these files in order:
1. `../CLAUDE.md` — Project rules and structure
2. `PROJECT-OVERVIEW.md` — What we're building and why
3. `ASSET-REFERENCE.md` — API endpoints, image URLs, example data (recently updated with set endpoint details)
4. `EXPERIMENT-2B-PLAN.md` — The step-by-step adjustment plan

Also review the existing `experiments/exp1/index.html` closely — you'll be reworking the arrow navigation and adding new interactions.

## What To Do

Work through `EXPERIMENT-2B-PLAN.md` starting at **Step 1**.

This is an **adjustment to the existing file**. You are modifying `experiments/exp1/index.html` in place.

**Output file:** `experiments/exp1/index.html` (relative to project root `/Users/cdevine/Developer/OPcard-experiments/`)

## Context: What's Changing

The left/right arrows currently cycle through card **variants** (Standard/Parallel/Alt Art). We're repurposing them:
- **Arrows + swipe** → now browse through all cards in the selected **set**
- **Long press (hold) on the card** → opens a variant tray for alternate versions
- A **set selector dropdown** is added to the bottom bar

## Rules (Non-Negotiable)

1. **ONE STEP AT A TIME.** Complete the current step, then STOP.
2. **Show your work.** After each step, describe what you built and confirm it's working.
3. **Wait for approval.** Do NOT proceed to the next step until the user says so.
4. **Modify the existing file.** Do NOT create a new file.
5. **Don't break existing features.** Tilt, flip, holo shimmer, auto demo, gyroscope — all must continue working. If something breaks, fix it before moving on.
6. **Keep it clean.** Remove dead code from the old variant arrow logic. Well-commented, CSS variables for new tunables.

## Start Now

Begin with **Step 1: Set Selector & Metadata Fetch** from `EXPERIMENT-2B-PLAN.md`.

---

## After 2B: Next Task

Once Experiment 2B is approved, the next task is **Experiment 3: Pack Opening Simulation**. That will be a new standalone file at `experiments/exp3/index.html`. The CC-PROMPT will be updated at that point.
