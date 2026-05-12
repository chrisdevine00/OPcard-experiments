# Experiment 2: Holographic Foil Shimmer — Build Plan

> **Pre-requisite:** Experiment 1 must be complete. This experiment extends `experiments/exp1/index.html` directly.
> Read `ASSET-REFERENCE.md` for API details if needed.

## Goal
Add a holographic foil shimmer effect to the existing 3D tilt card from Experiment 1. The effect combines a rainbow gradient shift with a fine holographic line texture, and only activates on cards with qualifying rarities. The holo reacts to the same mouse/gyroscope/auto-demo input that drives the tilt.

## Output
Modified file: `experiments/exp1/index.html` (relative to project root `/Users/cdevine/Developer/OPcard-experiments/`)

## Rarity Rules
The holo effect is **rarity-gated**. Only these rarities get the shimmer:
- `L` — Leader
- `R` — Rare
- `SR` — Super Rare
- `SEC` — Secret Rare
- `SP` — Special Rare
- Any Parallel variant (card name contains "Parallel" or image ID contains `_p1`)

These rarities get **no holo** (card displays as normal):
- `C` — Common
- `UC` — Uncommon

If the API fetch fails and rarity is unknown, default to **holo ON** (it's more fun).

---

## STEP 1: Rarity Detection & Holo State

Update the existing card loading logic to track rarity and holo eligibility:

1. When card data loads from the API, extract the `rarity` field
2. Define a `HOLO_RARITIES` constant: `['L', 'R', 'SR', 'SEC', 'SP']`
3. Also check if the card name includes "Parallel" or the `card_image_id` contains `_p1`
4. Set a boolean `isHoloEligible` on the card state
5. Add a **rarity badge** to the card info area below the card:
   - Small pill/tag showing the rarity abbreviation (e.g. "SR", "L", "C")
   - Color-coded: gold for SEC, silver for SR, bronze/copper for R, red for L, grey for C/UC
6. Add a CSS class `holo-eligible` to the card container when the card qualifies
7. When loading a new card via the card ID input, re-evaluate holo eligibility and update the class

Don't add any visual holo effect yet — just the detection, badge, and CSS class.

### ✅ Checkpoint 1
- [ ] Rarity badge displays below the card with correct color coding
- [ ] `holo-eligible` class is added/removed based on rarity
- [ ] Loading `OP01-001` (Leader) → holo-eligible, red "L" badge
- [ ] Test with a Common card if possible — no `holo-eligible` class
- [ ] Card ID swapping correctly re-evaluates rarity
- **STOP here. Show the result. Wait for approval.**

---

## STEP 2: Rainbow Gradient Overlay (Static)

Add the core holo rainbow gradient as a visual layer on the card front:

1. Create an overlay `<div>` positioned absolutely on top of the card front image
   - Same dimensions as the card, same rounded corners
   - `pointer-events: none` so it doesn't block click/tap interactions
2. Apply a **multi-color linear gradient** approximating holographic refraction:
   - Colors: a rainbow-ish sequence — e.g. `#ff0000, #ff8800, #ffff00, #00ff00, #0088ff, #8800ff, #ff0000`
   - The gradient should be larger than the card (e.g. 200–300% size) so it can be shifted around
3. Set `mix-blend-mode: color-dodge` (or experiment with `overlay`, `screen`, `soft-light` — use whichever looks best against card art)
4. Set opacity low (~0.15–0.25) so the card art is still clearly visible
5. **Only show this overlay when the card has the `holo-eligible` class** — otherwise hide it completely
6. For now, the gradient is **static** (centered, not reacting to mouse yet). Just get it looking right visually.

### ✅ Checkpoint 2
- [ ] Rainbow gradient overlay is visible on holo-eligible cards
- [ ] Card art is still clearly readable through the overlay
- [ ] The blend mode and opacity look good (not washed out, not invisible)
- [ ] Loading a non-holo card (C/UC) shows no overlay
- [ ] Overlay doesn't interfere with click-to-flip or other interactions
- **STOP here. Show the result. Wait for approval.**

---

## STEP 3: Gradient Reacts to Mouse / Tilt

Make the rainbow gradient shift position based on the existing tilt data:

1. The tilt engine from Experiment 1 already calculates X/Y rotation values from mouse position
2. Use those same values to shift the gradient's `background-position`:
   - As the cursor moves left → gradient shifts left, revealing different colors
   - As the cursor moves up → gradient shifts up
   - The mapping should feel natural — the "light source" moves with the viewer's perspective
3. Use CSS `transition` or direct style updates (matching the tilt timing) so the gradient movement is smooth and synced with the card tilt
4. On `mouseleave`, the gradient should return to its center/default position (matching the tilt returning to flat)
5. The rainbow shift should be **subtle but noticeable** — the goal is to simulate light catching a foil surface at different angles

### ✅ Checkpoint 3
- [ ] Moving the mouse over the card shifts the rainbow gradient
- [ ] The gradient movement is in sync with the card tilt
- [ ] Effect looks like light refracting off a foil surface
- [ ] Returns to default on mouse leave
- [ ] Smooth, no flickering or jumps
- **STOP here. Show the result. Wait for approval.**

---

## STEP 4: Fine Holographic Line Texture

Add a second overlay layer with a fine repeating line pattern that simulates the micro-texture of real holographic foil:

1. Create another overlay `<div>` (or use `::after` pseudo-element) on top of the rainbow gradient layer
   - Same positioning rules: absolute, full card size, `pointer-events: none`
2. Generate a **fine repeating line pattern** using CSS:
   - Option A: `repeating-linear-gradient` with very thin alternating transparent/semi-white lines (~1–2px lines, ~4–6px spacing)
   - Option B: Multiple layered gradients at different angles (e.g. 45° and -45° to create a crosshatch)
   - The lines should be subtle — visible on close inspection but not overpowering
3. This texture layer should also shift slightly with mouse movement, but at a **different rate** than the rainbow gradient (parallax between layers creates a more convincing holo illusion)
   - E.g. rainbow shifts at 1x rate, texture shifts at 0.5x rate
4. Set opacity/blend mode so the texture is barely visible on its own but interacts with the rainbow gradient layer to create depth
5. Only visible on `holo-eligible` cards

### ✅ Checkpoint 4
- [ ] Fine line texture is visible over the card on close inspection
- [ ] Texture moves at a different rate than the rainbow gradient (parallax)
- [ ] Combined effect of rainbow + texture looks like real holographic foil
- [ ] Not too heavy — card art is still the star
- [ ] Only appears on holo-eligible cards
- **STOP here. Show the result. Wait for approval.**

---

## STEP 5: Mobile Gyroscope & Auto Demo Integration

Connect the holo effect to the existing gyroscope and auto-demo systems:

1. **Gyroscope:** The tilt engine already smooths device orientation into X/Y rotation values. Feed those same values into the gradient and texture `background-position` shifts (same mapping as mouse in Step 3)
2. **Auto Demo:** When auto-demo mode is active, the tilt values are driven by the animation loop. The holo effect should automatically follow — no separate animation needed, just make sure the holo reads from the same tilt state
3. **Fallback tilt buttons:** If using the arrow button fallback on mobile, the preset tilt angles should also trigger corresponding holo gradient shifts
4. Verify that the holo effect works during:
   - Mouse tilt (desktop)
   - Gyroscope tilt (mobile)
   - Auto demo mode
   - Fallback button tilt
   - Card flip (holo should be visible on front, not on back)

### ✅ Checkpoint 5
- [ ] Tilting phone shifts the holo gradient (same as mouse on desktop)
- [ ] Auto demo mode drives the holo effect automatically
- [ ] Fallback buttons trigger holo shifts
- [ ] Holo only visible on the front face, not the card back
- [ ] All input modes feel consistent
- **STOP here. Show the result. Wait for approval.**

---

## STEP 6: Card Variant Browsing

Add navigation to browse alternate versions of the same card (Parallel, Alt Art, Manga Art, etc.):

### How it works
The API already returns **all variants** for a card ID as an array. For example, fetching `OP01-001` returns both the standard card and the Parallel version. Each entry has a different `card_name` and `card_image` but shares the same `card_set_id`.

### Variant label logic
Parse each variant's `card_name` to extract a human-friendly label:
- If name contains "(Parallel)" → label: **"Parallel"**
- If name contains "(Manga)" → label: **"Manga"**
- If name contains "(Alt Art)" or similar → label: **"Alt Art"**
- If `card_image_id` contains `_p1`, `_p2`, etc. → label: **"Parallel"** (fallback)
- The first entry with no special suffix → label: **"Standard"**
- If there's only one variant, don't show any navigation (no arrows needed)

### UI: Arrow Navigation
1. Add **left/right arrow buttons** on either side of the card (vertically centered)
   - Use simple `‹` / `›` or chevron icons
   - Semi-transparent, become more visible on hover/tap
   - Min 44px tap target for mobile
   - Hidden when only one variant exists
2. Display a **variant label** between the card name and rarity badge below the card:
   - e.g. "Standard" or "Parallel" — small, subtle text
   - Include a dot indicator showing position (e.g. `● ○` for 2 variants, showing you're on the first)
3. **Swipe support on mobile:** detect horizontal swipe gestures on the card area to cycle variants (in addition to arrow buttons)

### Behavior
1. Clicking right arrow / swiping left → load next variant with a smooth crossfade transition
2. Clicking left arrow / swiping right → load previous variant
3. Wrap around (last → first, first → last)
4. On variant change:
   - Swap the card image (smooth fade, same as card ID loading)
   - Update the variant label and dot indicator
   - **Re-evaluate holo eligibility** — a Parallel of a Common card should get holo treatment since Parallels are special prints
   - Update the rarity badge if the variant has different rarity info
5. When the user types a **new card ID** in the input field:
   - Fetch all variants for that card
   - Reset to the first (Standard) variant
   - Update arrow visibility based on how many variants exist
6. Swipe detection should NOT conflict with the tilt interaction — use a horizontal distance threshold (e.g. >50px horizontal movement with <30px vertical) to distinguish a swipe from general touch

### ✅ Checkpoint 6
- [ ] Arrow buttons appear when a card has multiple variants
- [ ] Arrows hidden when only one variant exists
- [ ] Clicking arrows cycles through variants with smooth crossfade
- [ ] Variant label and dot indicator update correctly
- [ ] Swipe works on mobile to cycle variants
- [ ] Holo eligibility re-evaluates per variant
- [ ] Loading a new card ID resets to Standard variant
- [ ] Swipe doesn't conflict with tilt interaction
- **STOP here. Show the result. Wait for approval.**

---

## STEP 7: Polish & Testing

Final pass on the complete holo implementation:

1. **Transitions:** When loading a new card that changes holo eligibility (e.g. switching from a Leader to a Common), the holo overlay should **fade in/out smoothly** rather than popping on/off
2. **Flip interaction:** During the flip animation, the holo overlay should fade out as the card rotates past 90° and not appear on the back face at all
3. **Performance:** Ensure the two overlay layers + tilt transforms aren't causing jank. Use `will-change` or `transform: translateZ(0)` if needed for GPU compositing
4. **Visual tuning:** Step back and evaluate the overall look:
   - Is the rainbow too strong or too subtle?
   - Does the line texture add to the illusion or distract?
   - Adjust opacity, blend modes, gradient colors, and line spacing as needed
5. **Test across cards:** Try several card IDs to make sure the holo looks good on different card art (light art, dark art, busy art):
   - `OP01-001` (Leader, red) — holo ON, has Parallel variant
   - `OP01-120` (SR) — holo ON
   - `OP02-001` (Leader, green) — holo ON
   - Try to find a C or UC card — holo OFF
   - Test variant arrows: cycle through Standard/Parallel on `OP01-001`, confirm holo re-evaluates
6. **Variant browsing edge cases:**
   - Card with only one variant — arrows should be hidden
   - Rapid arrow clicking — should not break transitions or double-load
   - Swipe + tilt on mobile — swipe to change variant, tilt to move card, no conflicts
7. **Code cleanup:** Comment the new holo and variant sections clearly, keep CSS variables for all holo tunables (opacity, gradient colors, texture spacing, shift multipliers) so they're easy to adjust later
8. **Responsive:** Holo and variant arrows look good on both mobile and desktop widths

### ✅ Checkpoint 7 (Final)
- [ ] Holo fades in/out smoothly on card swap and rarity change
- [ ] Holo disappears correctly during flip
- [ ] Variant arrows work correctly, hidden when only one variant
- [ ] Swipe navigation works on mobile without conflicting with tilt
- [ ] No performance issues
- [ ] Looks great across multiple card types
- [ ] All holo CSS values are in tunable variables
- [ ] Code is clean and ready for Experiment 3
- **Show final result. Review together before moving to Experiment 3.**
