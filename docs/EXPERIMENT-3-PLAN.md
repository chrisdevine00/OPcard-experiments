# Experiment 3: Pack Opening Simulation — Build Plan

> **Pre-requisite:** Experiments 1, 2, and 2B must be complete. This is a **new standalone file** that reuses the card component (tilt, flip, holo) from Experiment 1/2.

## Goal
Build a booster pack opening simulation. The user selects a set, tears open an animated pack, then taps to reveal cards one at a time. Rare cards get the full holo treatment from Experiment 2. The experience should feel exciting and satisfying — like opening a real pack.

## Output
New file: `experiments/exp3/index.html` (relative to project root `/Users/cdevine/Developer/OPcard-experiments/`)

This is a **single standalone HTML file** with embedded CSS and JS. It should contain its own copy of the card rendering logic (tilt, flip, holo) from `exp1/index.html` — not import or reference it. This keeps each experiment self-contained.

---

## STEP 1: Page Shell & Set Selector

Build the basic page structure:

1. Dark background matching the Experiment 1 aesthetic (same gradient, same CSS variables)
2. **Title/header:** "OPTCG Pack Opener" — centered, subtle styling
3. **Set selector:** a prominent dropdown or button group for choosing a set
   - List all available sets (see `ASSET-REFERENCE.md`)
   - Show set name and ID (e.g. "OP-01 — Romance Dawn")
   - Default: `OP-01`
4. **"Open Pack" button** — large, centered below the set selector
   - Disabled until a set is selected and data is loaded
   - Styled prominently — this is the main call to action
5. **Pack count area** — space below the button where pack contents will appear (empty for now)
6. On set selection, fetch all cards from `https://optcgapi.com/api/sets/{set_id}/`
   - Store the card data
   - Deduplicate to get unique base cards (same logic as Experiment 2B)
   - Show a loading state during fetch
   - Enable the "Open Pack" button once loaded
   - Handle CORS failure gracefully

### ✅ Checkpoint 1
- [ ] Page loads with dark themed layout
- [ ] Set dropdown works and fetches card data
- [ ] Open Pack button appears, disabled until data loads, enabled after
- [ ] Loading state during fetch
- [ ] Error handling for API failure
- **STOP here. Show the result. Wait for approval.**

---

## STEP 2: Pack Generation Logic

Build the logic that generates a realistic pack of cards when "Open Pack" is clicked:

1. A real OPTCG booster pack contains **12 cards** with this approximate rarity distribution:
   - 5 Common (C)
   - 3 Uncommon (UC)
   - 2 Rare (R)
   - 1 Super Rare (SR) — guaranteed
   - 1 slot that's either: another R, or a SEC/SP (low chance, ~10%)
2. Implement a `generatePack(setCards)` function that:
   - Groups available cards by rarity
   - Randomly selects cards following the distribution above
   - No duplicate cards within a single pack
   - The "bonus" 12th slot: 90% chance another R, 10% chance SEC or SP (if the set has them — if not, default to R)
   - Returns an ordered array of 12 card objects, sorted from lowest to highest rarity (C first, rarest last — this creates the classic pack opening tension)
3. For now, just **log the generated pack to the console** and display the card names in a simple text list on the page. No animations yet.
4. Clicking "Open Pack" again generates a new random pack.

### ✅ Checkpoint 2
- [ ] Clicking Open Pack generates 12 cards with realistic rarity distribution
- [ ] No duplicates within a pack
- [ ] Cards are ordered from Common to rarest
- [ ] The rare slot logic works (90/10 split)
- [ ] Results displayed as a text list (temporary — animations come later)
- [ ] Clicking again generates a different pack
- **STOP here. Show the result. Wait for approval.**

---

## STEP 3: Pack Visual & Tear Animation

Create the booster pack visual and the opening animation:

### Pack visual
1. Design a **booster pack wrapper** using CSS:
   - Rectangular shape, slightly taller than wide (~3:4 ratio)
   - Background: gradient or solid color themed to the selected set's primary color (red for OP-01, etc. — or use a generic dark theme with the set name on it)
   - Display the set name and "BOOSTER PACK" text on the front
   - Add a subtle foil/shine effect (CSS gradient overlay) to make it feel premium
   - The pack should be centered on the page, replacing the "Open Pack" button area
2. The pack appears after clicking "Open Pack"

### Tear animation
3. On **tap/click the pack**, play a tear-open animation:
   - The pack splits from the top — two halves peel apart and slide/rotate away
   - Use CSS `clip-path` or two overlapping divs that transform apart
   - Animation duration: ~0.8–1 second
   - Add a subtle "whoosh" feeling — maybe slight scale-up at the moment of tear
   - After the tear completes, the pack halves fade out
4. After the pack is opened, transition to the **card reveal area** (built in next steps)

### ✅ Checkpoint 3
- [ ] Booster pack visual appears centered and looks good
- [ ] Tapping the pack plays the tear animation
- [ ] Pack splits open convincingly
- [ ] After tearing, the pack disappears and the area is ready for card reveals
- [ ] Animation is smooth and satisfying
- **STOP here. Show the result. Wait for approval.**

---

## STEP 4: Card Reveal — One at a Time

Build the card-by-card reveal sequence:

### Card reveal area
1. After the pack opens, show a **face-down card** centered on screen
   - Use the same card back design from Experiment 1 (dark red, pattern, "ONE PIECE CARD GAME")
   - Same card dimensions as Experiment 1 (5:7 ratio, ~280–320px wide)
2. Display a counter: "Card 1 of 12"

### Tap to reveal
3. **Tap/click the face-down card** to flip it over (reveal the card):
   - Use the same flip animation from Experiment 1 (~0.6s, Y-axis rotation)
   - Once revealed, the card shows its front image with full tilt interaction
   - If the card is holo-eligible (R, SR, SEC, SP, L), apply the holo shimmer from Experiment 2
   - Display the card name and rarity below
4. After revealing, show a **"Next Card"** button (or allow tap/swipe to advance)
5. Tapping "Next Card":
   - Current card slides/fades out to the left
   - A new face-down card slides in from the right
   - Counter updates ("Card 2 of 12")
6. The user continues tapping through all 12 cards

### ✅ Checkpoint 4
- [ ] Face-down card appears after pack opens
- [ ] Tapping flips it to reveal the card image
- [ ] Tilt interaction works on the revealed card
- [ ] Holo shimmer appears on eligible rarities
- [ ] "Next Card" advances to the next face-down card with transition
- [ ] Counter tracks progress (1 of 12, 2 of 12, etc.)
- [ ] All 12 cards can be revealed in sequence
- **STOP here. Show the result. Wait for approval.**

---

## STEP 5: Rare Card Emphasis

Make rare card reveals feel special:

1. **Before flipping a rare card** (SR, SEC, SP), add a brief dramatic build-up:
   - The card back glows or pulses with a colored light for ~1 second before flipping
   - Glow color could be based on rarity: silver for SR, gold for SEC/SP
   - This signals to the user "something special is coming" — builds tension
2. **After revealing a rare card:**
   - Brief burst of CSS particle/sparkle effect around the card (use CSS animations with small pseudo-elements or generated divs that float outward and fade)
   - The holo shimmer should be more intense on SEC/SP cards (increase the rainbow overlay opacity slightly)
3. **The last card** (the rarest in the pack) gets the biggest treatment:
   - Longer glow build-up (~1.5s)
   - More particles on reveal
   - The card could briefly scale up slightly (105%) on reveal before settling back to normal
4. These effects should be purely CSS/JS — no external particle libraries

### ✅ Checkpoint 5
- [ ] SR cards have a brief glow before flip
- [ ] SEC/SP cards have a longer, more dramatic glow
- [ ] Sparkle particles appear on rare card reveals
- [ ] The final card (rarest) has the most dramatic reveal
- [ ] Effects are CSS/JS only, no external libraries
- [ ] Effects don't interfere with tilt or holo
- **STOP here. Show the result. Wait for approval.**

---

## STEP 6: Pack Summary & Reset

After all 12 cards are revealed, show a summary:

1. **Summary screen** — display all 12 cards in a grid/fan layout:
   - Small thumbnails (~80–100px wide) of all revealed cards
   - Arranged in a grid (4×3) or a curved fan/arc
   - Rare cards should have a subtle glow border to stand out
   - Tap any thumbnail to view that card full-size (reuse the tilt + holo card viewer)
2. **Stats line:** "You pulled: 5C, 3UC, 2R, 1SR, 1SEC" or similar
3. **"Open Another Pack"** button — resets to the pack visual and generates a new random pack from the same set
4. **Set selector** remains accessible to switch sets and open packs from a different set
5. **Back button or "View Card"** — tapping a thumbnail from the summary brings up the full card viewer with tilt + holo, with a "Back to Summary" option

### ✅ Checkpoint 6
- [ ] Summary screen shows all 12 cards after the last reveal
- [ ] Stats line accurately counts rarities
- [ ] Tapping a thumbnail shows the full card with tilt + holo
- [ ] "Open Another Pack" generates a new pack and resets
- [ ] Set selector still works to switch sets
- **STOP here. Show the result. Wait for approval.**

---

## STEP 7: Mobile Experience & Polish

Final pass for mobile optimization and overall polish:

1. **Touch interactions:**
   - Tap face-down card to reveal (not just button)
   - Swipe left to advance to next card (alternative to "Next Card" button)
   - All tilt + holo effects work via gyroscope on revealed cards
   - Pack tear works on tap
2. **Responsive layout:**
   - Pack, cards, and summary grid all look good at 375px, 768px, 1200px+
   - Bottom controls don't overlap content
   - Summary grid adjusts columns for screen width (2×6 on mobile, 4×3 on desktop)
3. **Transitions:**
   - All state changes are smooth (pack → tear → reveal → summary)
   - No jarring jumps between screens
4. **Performance:**
   - Preload all 12 card images while the pack tear animation plays (user won't notice the loading time)
   - Particle effects don't cause frame drops
   - `will-change` on animated elements
5. **Code structure:**
   - Well-commented sections: pack generation, pack visual, tear animation, card reveal, rare effects, summary
   - CSS variables for all tunables (animation durations, glow colors, particle counts)
   - Reusable card renderer (tilt + flip + holo) cleanly separated from pack-specific logic
6. **Test the full flow end to end:**
   - Select OP-01 → Open Pack → tear animation → reveal 12 cards one by one → summary → tap thumbnail → full card → back to summary → Open Another Pack → repeat
   - Try with OP-05 or another set to verify set switching works

### ✅ Checkpoint 7 (Final)
- [ ] Full flow works end to end on desktop and mobile
- [ ] Touch/swipe interactions are smooth
- [ ] All 12 cards load without delay (preloaded during tear)
- [ ] Rare reveals feel dramatic and exciting
- [ ] Summary screen is useful and tappable
- [ ] Responsive at all breakpoints
- [ ] Code is clean and well-organized
- **Show final result for review.**
