# Experiment 2B: Set Browsing & Variant Tray — Adjustment Plan

> **Context:** This adjusts the existing `experiments/exp1/index.html` before Experiment 3 begins. The left/right arrows currently cycle through card variants (Standard/Parallel). We're repurposing them to browse through an entire set, and moving variant access to a long-press interaction.

## Output
Modified file: `experiments/exp1/index.html` (relative to project root `/Users/cdevine/Developer/OPcard-experiments/`)

---

## STEP 1: Set Selector & Metadata Fetch

Add a set selector and fetch the full card list for a set:

1. Add a **dropdown/select element** to the bottom control bar listing available sets:
   - Populate with known set IDs and names (see `ASSET-REFERENCE.md` for the full list)
   - Default selection: `OP-01` (Romance Dawn)
   - Styled to match the existing dark theme
2. When a set is selected, fetch all cards from `https://optcgapi.com/api/sets/{set_id}/`
3. **Deduplicate the response** to build two data structures:
   - `setCardList` — an ordered array of unique base cards (one entry per card number, sorted by `card_set_id`). This is what the arrows browse through.
   - `variantMap` — a lookup object keyed by `card_set_id`, where each value is an array of all variants for that card (standard, parallel, alt art, etc.)
   - To deduplicate: group by `card_set_id`. The first entry in each group (no `_p` suffix in `card_image_id`) is the base card for `setCardList`. All entries go into `variantMap`.
4. Show a **loading indicator** while the set data fetches
5. Once loaded, display the first card in the set and show a **position counter**: "1 / 121" (or however many unique cards)
6. If the API call fails (CORS), fall back gracefully — show an error message suggesting the user type card IDs manually

### ✅ Checkpoint 1
- [ ] Set dropdown appears in the control bar with all sets listed
- [ ] Selecting a set fetches the card list and shows the first card
- [ ] Position counter displays correctly (e.g. "1 / 121")
- [ ] Loading indicator appears during fetch
- [ ] Existing card display, tilt, flip, holo all still work
- **STOP here. Show the result. Wait for approval.**

---

## STEP 2: Repurpose Arrows for Set Browsing

Change the left/right arrows from variant cycling to set browsing:

1. **Remove the old variant cycling logic** from the arrow buttons and swipe handlers
2. **Right arrow / swipe left** → advance to the next card in `setCardList`
3. **Left arrow / swipe right** → go to the previous card in `setCardList`
4. Wrap around (last card → first, first card → last)
5. On card change:
   - Smooth crossfade transition (reuse existing card swap animation)
   - Update position counter ("34 / 121")
   - Update card info (name, rarity badge)
   - Re-evaluate holo eligibility for the new card
   - Load the **standard version** (first variant) by default
   - Update the variant indicator (see Step 3) for the new card
6. **Sliding window image preload:**
   - Maintain a window of 5 images: 2 behind current, current, 2 ahead
   - When the user arrows forward, preload the next images that enter the window
   - Use `new Image()` objects to trigger browser caching — just set `.src`, don't append to DOM
   - If the user arrows to a card whose image isn't loaded yet, show the loading pulse on the card area until it's ready
   - On set change, clear the preload cache and start fresh with the first card's window
7. The **card ID text input** should still work — typing a card ID jumps to that card within the current set (find its index in `setCardList`). If the card ID isn't in the current set, attempt a direct fetch as before.

### ✅ Checkpoint 2
- [ ] Left/right arrows now browse through cards in the selected set
- [ ] Position counter updates on each card change
- [ ] Crossfade transition is smooth
- [ ] Holo eligibility updates per card
- [ ] Preload window works — next/previous cards appear instantly
- [ ] Cards outside the preload window show a brief loading state
- [ ] Card ID input still works (jumps within set or fetches directly)
- [ ] Swipe on mobile browses the set (not variants)
- **STOP here. Show the result. Wait for approval.**

---

## STEP 3: Long-Press Variant Tray

Replace the old arrow-based variant cycling with a long-press interaction:

### Long-press detection
1. On **mouse down / touch start** on the card, start a timer (~500ms)
2. If the user releases before 500ms → treat as a normal click (flip the card)
3. If the timer completes (500ms held) → open the variant tray
4. Cancel the timer if the user moves their finger/mouse significantly (>10px) — that's a drag/swipe, not a long press
5. **Important:** long press should NOT trigger the flip animation

### Variant tray UI
1. When triggered, show a **horizontal tray/shelf** that slides up from below the card (or overlays the bottom portion of the screen)
2. The tray contains **thumbnail previews** of all variants for the current card:
   - Small card thumbnails (~60–80px wide, maintaining 5:7 ratio)
   - The current variant has a highlighted border (glow or bright outline)
   - Each thumbnail has a label below it: "Standard", "Parallel", "Alt Art", etc.
   - If only one variant exists, show the tray with just that one thumbnail and a message like "No alternate versions"
3. **Tap a thumbnail** to select that variant:
   - Swap the main card image with a smooth crossfade
   - Update card info, rarity badge, holo eligibility
   - Close the tray
4. **Tap outside the tray** or **tap a close button** to dismiss without changing
5. Tray has a semi-transparent dark backdrop (same style as the bottom control bar)
6. Show a subtle **hint indicator** on cards that have multiple variants:
   - A small icon or badge in the corner of the card (e.g. a tiny layers/stack icon)
   - Only visible when >1 variant exists
   - Helps users discover the long-press feature

### Variant label logic (carried over from Experiment 2)
- Card name contains "(Parallel)" → "Parallel"
- Card name contains "(Manga)" → "Manga"  
- `card_image_id` contains `_p1`, `_p2` → "Parallel", "Parallel 2"
- No special suffix → "Standard"

### ✅ Checkpoint 3
- [ ] Long press (500ms) on the card opens the variant tray
- [ ] Short tap still flips the card (not affected by long press)
- [ ] Tray shows thumbnails of all variants with labels
- [ ] Tapping a thumbnail swaps the main card and closes the tray
- [ ] Tapping outside closes the tray
- [ ] Variant hint icon appears on cards with multiple variants
- [ ] Cards with only one variant show "No alternate versions" in tray
- [ ] Works on both desktop (mouse hold) and mobile (touch hold)
- **STOP here. Show the result. Wait for approval.**

---

## STEP 4: Polish & Integration Testing

Final pass on the set browsing and variant tray:

1. **Interaction conflicts:** Verify there are no conflicts between:
   - Short tap → flip
   - Long press → variant tray
   - Swipe → set browsing
   - Mouse hover → tilt
   - All should coexist cleanly
2. **Set switching:** Changing the set dropdown should:
   - Fetch the new set
   - Reset to card 1
   - Clear preload cache
   - Update position counter
   - Close the variant tray if open
3. **Performance:** The full set metadata fetch should be fast. If a set has 200+ entries (with variants), make sure the deduplication and sorting don't block the UI.
4. **Edge cases:**
   - Very first card in set → left arrow wraps to last
   - Very last card → right arrow wraps to first
   - Rapid arrow clicking → transitions don't stack or break
   - Opening variant tray during auto-demo → pause auto-demo, resume on close
5. **Discoverability:** First-time users may not know about long press. Add a subtle tooltip or hint text below the card on first load: "Long press card for alternate versions" — dismiss after first use or after 5 seconds
6. **Code cleanup:** Clean up any dead variant-cycling code from the old arrow implementation

### ✅ Checkpoint 4 (Final)
- [ ] No interaction conflicts between tap, long press, swipe, hover, and tilt
- [ ] Set switching works cleanly
- [ ] Preload window keeps browsing fast
- [ ] Variant tray works on desktop and mobile
- [ ] Tooltip/hint helps discoverability
- [ ] All Experiment 1 and 2 features still work
- [ ] Code is clean with old variant arrow code removed
- **Show final result. Review together before moving to Experiment 3.**
