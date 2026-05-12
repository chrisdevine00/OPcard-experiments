# Step 4 Revisions — Card Reveal (Rewritten)

This replaces the current Step 4 implementation. Delete what's there and rebuild based on this spec.

---

## The Real-Life Experience We're Recreating

When you cut open a real OPTCG booster pack:
1. You slice across the top
2. The front of the first card is immediately visible — cards are packed face-up
3. You slide the wrapper down and off the cards, revealing the full card front
4. You look at the card, then pull it off the stack to see the next one

That's exactly what this should feel like.

---

## 1. Fix Card Images First

Card images aren't loading. Debug before anything else:

1. Check the console for CORS errors or 404s
2. Card image URLs follow this pattern: `https://optcgapi.com/media/static/Card_Images/{card_set_id}.jpg`
3. If the API fetch for card data is failing (CORS), construct image URLs directly from `card_set_id` (e.g. `OP01-001` → `https://optcgapi.com/media/static/Card_Images/OP01-001.jpg`)
4. Verify the pack array from `generatePack()` has valid image data — log it
5. Add `onerror` on every card `<img>` to show card ID as text fallback

**Don't proceed to the visual work until card images are confirmed loading.**

---

## 2. The Reveal Moment — Wrapper Slides Off the Cards

After the top flap tears away (Step 3), the bottom wrapper should slide down **off the card stack**, revealing the front of the first card from top to bottom.

### What the user sees:

1. **Top flap tears upward** (existing Step 3 animation)
2. **The top edges of the card stack become visible** — you can see the front face of card #1 peeking out at the top as the wrapper begins to separate
3. **Bottom wrapper slides DOWN at full opacity** — it moves down and progressively reveals the first card's face from top to bottom, like pulling a sleeve off a stack
4. **The wrapper stays fully opaque until it clears the cards** — only AFTER it has completely slid past the bottom edge of the cards does it begin to fade out. This is important: the wrapper shouldn't go transparent while it's still overlapping the cards
5. **The first card is now fully visible — face-up.** This IS the reveal moment. No flip needed.

### Technical implementation:

- The card stack (Layer 1) should have the **first card's front image** as the top element, not a card back
- Stack the first card's `<img>` on top, with 2-3 more card fronts behind it (offset ~3px down, ~1-2px right each) to show the stack depth
- The bottom wrapper (Layer 2) slides down via `transform: translateY(...)` 
- Use a two-phase animation on the wrapper:
  - Phase 1 (0% → 70%): `translateY` moves down, `opacity: 1` (stays fully opaque while overlapping cards)
  - Phase 2 (70% → 100%): continues moving down past the card edges, `opacity` fades from 1 → 0
- The wrapper needs to travel far enough that it fully clears the card area before fading

---

## 3. Cards Scale Up After Reveal

Once the wrapper is gone and the first card is fully revealed:

1. The card stack **scales up slightly** (~10-15%) and moves closer to the viewer — use `transform: scale(1.1)` with a smooth ~0.4s ease-out transition
2. This creates a satisfying "now let's look at this" moment — the cards go from being inside the pack to being the main focus
3. The card should be centered on screen at a comfortable viewing size (~280-320px wide, same as Experiment 1)

---

## 4. Card Info Builds In Below

After the card scales up, the card's stats/info should animate in below the card:

1. **Card name** — the primary text, appears first
2. **Card ID** (e.g. "OP01-120") — smaller, secondary
3. **Rarity badge** — same color-coded pill from Experiment 2 (gold for SEC, silver for SR, etc.)
4. These should **fade + slide up** into position (~0.3s, staggered by ~0.1s each) — not appear instantly
5. If the card is holo-eligible (R, SR, SEC, SP, L), the holo shimmer effect from Experiment 2 should activate on the card at this point

---

## 5. Navigating Through Cards — Swipe Up + Button

After viewing the revealed card, the user advances to the next one:

### Swipe gesture:
- **Swipe UP** on the card → current card slides up and off screen
- The next card rises up from the stack below into the hero position
- Swipe threshold: ~50px vertical, <30px horizontal
- Do NOT use left/right swipe

### Button:
- **"Next Card"** button below the card info area
- Triggers the same animation as swipe up
- On the last card (12 of 12), button text changes to "View Results"

### Counter:
- Display **"Card X of 12"** — update on each advance
- Position it above the card or integrated into the header area

### Animation sequence for advancing:
1. Current card + its info slide **upward** and fade out (~0.3s)
2. The next card (already visible in the stack behind) scales up to the hero position (~0.3s)
3. Its info builds in below (same fade + slide as #4 above)
4. Stack shrinks by one card (one fewer offset layer behind)
5. Holo re-evaluates for the new card

### Stack visual throughout:
- Behind the current hero card, show up to 3-4 offset card fronts from the remaining pack
- Each offset ~3px down and ~1-2px right
- As cards are pulled off, the stack shrinks
- Last card has no stack behind it — just the single card

---

## 6. No Flip Interaction

**Do NOT implement flip-to-reveal.** The cards are face-up in the pack just like real life. The reveal moment is the wrapper sliding off. There is no card back shown at any point during the reveal sequence.

The tilt interaction from Experiment 1 CAN still work on the revealed card (mouse hover tilts it in 3D), but flip is removed entirely from this experiment.

---

## Code Architecture: Pre-Built Card Stack

**This is the key to smooth transitions.** The reason the previous implementation had jarring jumps is that cards were being created/moved in the DOM during transitions. Instead:

### All 12 cards exist in the DOM from the start

When `generatePack()` runs, immediately build all 12 card elements in a `.card-stack` container. Each card is absolutely positioned, stacked on top of each other:

```html
<div class="card-stack">
  <div class="stack-card" data-index="0" style="--i: 0"> <img src="..."> </div>
  <div class="stack-card" data-index="1" style="--i: 1"> <img src="..."> </div>
  <!-- ... all 12 ... -->
</div>
```

### Position via CSS custom property

```css
.stack-card {
  position: absolute;
  top: 0; left: 0;
  width: 100%;
  transition: transform 0.35s ease-out, opacity 0.35s ease-out;
  transform: translateY(calc(var(--i) * 2px));
  z-index: calc(12 - var(--i));
}
```

Card 0 (the top) sits at `translateY(0)`. Card 1 at `2px`. Card 2 at `4px`. The stack offset is visible but subtle.

Only show the top 4-5 cards visually. Cards beyond that get `opacity: 0`.

### Advancing — no jump

When "Next Card" is tapped:

1. **Current top card** gets a `.departing` class:
   ```css
   .stack-card.departing {
     transform: translateY(-110%) scale(0.95);
     opacity: 0;
     pointer-events: none;
   }
   ```
   It slides up and fades out via CSS transition.

2. **All remaining cards** get their `--i` decremented by 1:
   - Card 1: `--i` goes from `1` → `0` (moves from 2px → 0px)
   - Card 2: `--i` goes from `2` → `1` (moves from 4px → 2px)
   - Since `.stack-card` has `transition: transform 0.35s`, this is a smooth slide — **not a jump**

3. **Card info** fades out, then rebuilds with new card data

4. After the transition (~0.4s), hide the departed card with `display: none`

**Why this works:** Card 2 is ALREADY in the DOM, ALREADY at `translateY(2px)`. When it transitions to `translateY(0px)`, that's a smooth 2px slide happening simultaneously with Card 1's departure. There is never a frame where the hero position is empty.

---

## After applying these fixes

Show the result. Verify:
- [ ] Card images load correctly (test with OP-01)
- [ ] Pack opening reveals card fronts (face-up), not card backs
- [ ] Bottom wrapper slides down at full opacity, only fading after it clears the cards
- [ ] The reveal feels like pulling a sleeve off a stack of cards
- [ ] After reveal, cards scale up slightly for better viewing
- [ ] Card name, ID, and rarity badge animate in below the card
- [ ] Holo shimmer activates on eligible cards after reveal
- [ ] Swipe up advances to next card
- [ ] "Next Card" button also works
- [ ] Card counter tracks progress (Card 1 of 12, etc.)
- [ ] Card stack is visible behind current card and shrinks over time
- [ ] No flip interaction exists — cards are always face-up
- [ ] After card 12, ready to transition to summary

**STOP here. Show the result. Wait for approval.**
