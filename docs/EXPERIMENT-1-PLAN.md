# Experiment 1: 3D Card Tilt & Flip — Build Plan

> **Pre-requisite:** Read `ASSET-REFERENCE.md` for API details and card image URLs before starting.

## Output
Single file: `experiments/exp1/index.html` (relative to project root `/Users/cdevine/Developer/OPcard-experiments/`)

---

## STEP 1: Static Card Display

Build a single HTML file (embedded CSS + JS, no frameworks) that:

1. Dark background with subtle gradient (`#0a0a0a` to `#1a1a2e`)
2. Fetch card data from the API for card `OP01-001` on page load
3. Display the card image centered on the page
   - Aspect ratio: 5:7 (real TCG card proportions)
   - Width: ~280px mobile, ~320px desktop
   - Subtle rounded corners (4–6px), soft glow or thin bright border
4. Display card info below: card name, set ID, rarity (clean white/light text)
5. Loading state while image loads (pulsing outline or spinner)
6. Handle API failure gracefully — if CORS blocks metadata fetch, load image directly via URL pattern and show card ID as label (see `ASSET-REFERENCE.md` for CORS strategy)

**Use CSS custom properties** at the top for colors, sizes, timing — we build on this file in every step.

### ✅ Checkpoint 1
- [ ] Card image displays centered on dark background
- [ ] Card info text appears below
- [ ] Loading state works
- [ ] Error handling works if API is down
- [ ] Code is clean and well-commented
- **STOP here. Show the result. Wait for approval.**

---

## STEP 2: Mouse Tilt (Desktop)

Add 3D tilt on mouse hover:

1. Wrap card in a container with CSS `perspective: 900px`
2. On `mousemove` over card: calculate cursor position relative to card center
3. Map to `rotateX()` (vertical) and `rotateY()` (horizontal). Max: ±18 degrees
4. Apply via `transform` with CSS transition (~150ms ease-out)
5. On `mouseleave`: smoothly return to flat (0, 0)
6. Dynamic `box-shadow` shifting opposite to tilt direction

Keep tilt logic in a clearly separated, commented code section.

### ✅ Checkpoint 2
- [ ] Hovering tilts the card in 3D following the cursor
- [ ] Shadow shifts opposite to tilt
- [ ] Smooth return to flat on mouse leave
- [ ] No jitter
- **STOP here. Show the result. Wait for approval.**

---

## STEP 3: Card Flip

Add click-to-flip:

1. Two-sided card structure with `backface-visibility: hidden`:
   - **Front:** card image
   - **Back:** dark red/maroon, CSS-generated pattern (diamonds or crosshatch), "ONE PIECE CARD GAME" centered in gold/cream
2. Click/tap flips 180° on Y axis. ~0.6s `ease-in-out`
3. Click again flips back (toggle state)
4. Tilt from Step 2 must still work on both faces

### ✅ Checkpoint 3
- [ ] Click flips card with smooth animation
- [ ] Card back looks polished
- [ ] Click again flips back
- [ ] Tilt works on both sides
- **STOP here. Show the result. Wait for approval.**

---

## STEP 4: Bottom Control Bar + Flip Button

Add a fixed bottom control bar:

1. Fixed to viewport bottom
2. Semi-transparent dark background, `backdrop-filter: blur`, rounded top corners
3. Must NOT overlap the card (add bottom padding to main content)
4. Add **"Flip Card"** button (min 44px height, thumb-friendly)
5. Structure HTML/CSS so adding more buttons later is easy

### ✅ Checkpoint 4
- [ ] Bottom bar visible and styled
- [ ] Flip button works
- [ ] No overlap with card
- [ ] Looks good at desktop and mobile widths
- **STOP here. Show the result. Wait for approval.**

---

## STEP 5: Card ID Input & Loading

Add card switcher to the bottom bar:

1. Text input (placeholder: `OP01-001`) + "Load" button
2. On Load (or Enter key):
   - Basic format validation
   - Fade out current card
   - Fetch new card / construct image URL
   - Show loading indicator on card area
   - Fade in new card once loaded
   - Update card info text
3. On error: show "Card not found" message, keep previous card

### ✅ Checkpoint 5
- [ ] Can type a card ID and load a different card
- [ ] Smooth fade transition
- [ ] Loading state while new card loads
- [ ] Error handling for invalid IDs
- **STOP here. Show the result. Wait for approval.**

---

## STEP 6: Auto Demo Mode

Add "Auto Demo" toggle to bottom bar:

1. Button toggles between "Auto Demo" / "Stop Demo"
2. When active: animate card through a smooth looping tilt pattern
   - Use `requestAnimationFrame`
   - Figure-8 or circular path, ~4–5 second cycle
   - Should look like gentle floating/rotating
3. Stops when:
   - User clicks "Stop Demo"
   - User moves mouse over card
   - User taps card

### ✅ Checkpoint 6
- [ ] Auto Demo button starts smooth loop
- [ ] Animation looks natural
- [ ] Stops on user interaction
- [ ] Button text toggles correctly
- **STOP here. Show the result. Wait for approval.**

---

## STEP 7: Mobile Gyroscope Tilt

Add device orientation support:

1. Request `DeviceOrientationEvent` permission (iOS 13+ needs `requestPermission()` on user gesture)
2. Map orientation to tilt:
   - `beta` (front-back) → card X rotation
   - `gamma` (left-right) → card Y rotation
   - Clamp to ±18 degrees
3. Smooth with lerp: `smoothed = smoothed + (raw - smoothed) * 0.1` via `requestAnimationFrame`
4. If gyroscope unavailable/denied: show fallback buttons in bottom bar
   - Arrow buttons: ↑ ↓ ← → plus "Reset"
   - Each applies preset tilt angle
   - Only visible when gyroscope is not available

### ✅ Checkpoint 7
- [ ] Phone tilt moves the card on mobile
- [ ] Smooth, not jittery
- [ ] iOS permission request works
- [ ] Fallback buttons appear when needed
- [ ] Desktop behavior unchanged
- **STOP here. Show the result. Wait for approval.**

---

## STEP 8: Final Polish & Testing

Final pass:

1. **Responsive:** test at 375px, 768px, 1200px+ widths
2. **Header:** subtle "OPTCG Card Viewer" title at top
3. **Transitions:** all state changes smooth (loading, flip, card swap)
4. **Performance:** no layout thrashing, `requestAnimationFrame` where needed
5. **Code cleanup:** consistent formatting, section comments, no debug code
6. **Test scenarios:**
   - Page load → card appears with loading state
   - Mouse tilt → smooth
   - Click to flip → smooth both directions
   - Flip button → same as click
   - Load `OP02-001` → new card fades in
   - Load `XXXXX` → error handled
   - Auto Demo → smooth loop, stops on interaction
   - (Mobile) Gyroscope tilt → smooth
   - (Mobile) All buttons reachable

### ✅ Checkpoint 8 (Final)
- [ ] Everything works together
- [ ] Polished on desktop and mobile
- [ ] Code is clean and extensible for Experiment 2
- **Show final result. Review together before moving to Experiment 2.**
