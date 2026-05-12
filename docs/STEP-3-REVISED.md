# Experiment 3 — STEP 3 REVISED: Pack Visual & Tear-From-Top Animation

> **This replaces the original Step 3 in EXPERIMENT-3-PLAN.md.**
> Steps 1–2 are already complete (page shell, set selector, pack generation logic).
> Step 4 expects: after the tear completes, the pack is gone and the area is ready for card reveals.

---

## Why This Revision

The original Step 3 described a CSS-drawn rectangle that splits down the center like a door. Two problems:

1. **The pack looks fake** — a colored box with rounded corners and text looks nothing like a real OPTCG booster pack.
2. **The opening is wrong** — real OPTCG packs are opened by tearing/cutting across the top, not splitting down the middle.

This revision uses **real pack artwork** from the official OPTCG website and a **tear-from-top animation** driven by CSS `clip-path: polygon()`.

---

## Architecture: Three Layers

The pack is built from three stacked layers inside a single container:

```
┌─────────────────────────┐
│  LAYER 3: Top flap      │  ← The torn-off top portion. Animates UP and fades out.
│  (top ~15% of pack)     │     Has a jagged bottom edge via clip-path.
├─ ─ ─ TEAR LINE ─ ─ ─ ─ ┤
│  LAYER 2: Bottom wrap   │  ← The remaining pack wrapper. Animates DOWN and fades out.
│  (bottom ~85% of pack)  │     Has a jagged top edge (mirror of flap's bottom edge).
│                         │
│                         │
├─────────────────────────┤
│  LAYER 1: Card stack    │  ← Hidden behind the wrapper. Revealed as layers 2 & 3 leave.
│  (what's "inside")      │     A simple visual: stack of face-down cards or a solid block.
└─────────────────────────┘
```

All three layers occupy the same space via `position: absolute` within the pack container.

---

## Sub-step 3A: Pack Wrapper Image

### Where pack images come from (sources checked)

We checked all known OPTCG data sources for booster pack wrapper artwork:

- **OPTCG API** (`optcgapi.com`) — No pack images. The `allSets` endpoint returns only `set_name` and `set_id`. Card images only.
- **Limitless TCG** (`onepiece.limitlesstcg.com`) — Card database only. No pack wrapper art.
- **onepiece.gg** — Products and sets pages load dynamically via JS. No extractable static pack image URLs.
- **Official site** (`en.onepiece-cardgame.com`) — **This is the only reliable source.** Two URL patterns exist:
  1. **Product shot** (larger, higher quality): `/renewal/images/products/boosters/{slug}/img_item01.webp`
  2. **Thumbnail** (smaller, from the products listing page): `/images/products/boosters/{slug}/img_thumbnail.png`

The `{slug}` varies between sets — some use just the set code (e.g. `op01`, `op13`), while newer sets use combined codes (e.g. `op14-eb04`).

### Image fallback chain (try in order)

1. **Primary:** `img_item01.webp` — the full product packaging shot (best quality)
2. **Secondary:** `img_thumbnail.png` — the smaller thumbnail from the products listing (still a real pack image)
3. **Final fallback:** CSS-designed pack wrapper (if both image loads fail)

**Strategy: build a URL lookup map with a cascading onerror fallback.**

Define a `PACK_IMAGE_MAP` object that maps set IDs to both their primary and thumbnail URLs. Each `<img>` element has an `onerror` handler: if the primary fails, try the thumbnail; if the thumbnail also fails, hide the image and show the CSS fallback.

```javascript
const PACK_IMAGE_MAP = {
  'OP-01': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op01/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op01/img_thumbnail.png',
  },
  'OP-02': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op02/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op02/img_thumbnail.png',
  },
  'OP-03': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op03/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op03/img_thumbnail.png',
  },
  'OP-04': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op04/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op04/img_thumbnail.png',
  },
  'OP-05': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op05/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op05/img_thumbnail.png',
  },
  'OP-06': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op06/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op06/img_thumbnail.png',
  },
  'OP-07': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op07/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op07/img_thumbnail.png',
  },
  'OP-08': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op08/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op08/img_thumbnail.png',
  },
  'OP-09': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op09/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op09/img_thumbnail.png',
  },
  'OP-10': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op10/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op10/img_thumbnail.png',
  },
  'OP-11': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op11/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op11/img_thumbnail.png',
  },
  'OP-12': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op12/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op12/img_thumbnail.png',
  },
  'OP-13': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op13/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op13/img_thumbnail.png',
  },
  'OP-14': {
    primary: 'https://en.onepiece-cardgame.com/renewal/images/products/boosters/op14/img_item01.webp',
    thumb: 'https://en.onepiece-cardgame.com/images/products/boosters/op14-eb04/img_thumbnail.png',
  },
};
```

> **Note on OP-14:** The API uses set_id `OP14-EB04` for this set. The primary image slug is `op14` but the thumbnail slug is `op14-eb04`. Both are included in the map above.

> **Important**: Some of these URLs may 404 (especially older sets or if hotlink protection kicks in). The `<img>` `onerror` handler must cascade: primary → thumbnail → CSS fallback.

### onerror cascade implementation

```javascript
function loadPackImage(imgElement, setId) {
  const urls = PACK_IMAGE_MAP[setId];
  if (!urls) {
    // No URLs for this set — go straight to CSS fallback
    showCssFallback();
    return;
  }
  
  imgElement.src = urls.primary;
  imgElement.onerror = function() {
    // Primary failed, try thumbnail
    imgElement.src = urls.thumb;
    imgElement.onerror = function() {
      // Thumbnail also failed, use CSS fallback
      imgElement.style.display = 'none';
      showCssFallback();
    };
  };
}
```

### CSS fallback pack design

If the real image doesn't load, show a **CSS-designed wrapper** that still looks convincing:

- **Shape**: rectangular, roughly **200px wide × 280px tall** (or responsive equivalent), matching real booster pack proportions (~5:7)
- **Background**: a rich gradient based on the set's theme color. Use the `SET_COLORS` lookup (see Sub-step 3E below) or default to deep crimson-to-dark (the One Piece brand red)
- **Content**: 
  - "ONE PIECE" text near the top in gold/cream, small caps
  - "CARD GAME" below it, smaller
  - Set name (e.g. "ROMANCE DAWN") centered, larger, white
  - "BOOSTER PACK" below in smaller text
  - A subtle foil-shine CSS effect: a diagonal `linear-gradient` overlay with a semi-transparent white-to-transparent band, animated slowly on loop to simulate light catching foil packaging
- **Border**: NO rounded corners (real packs have sharp sealed edges). Add a subtle drop shadow for depth.

### Applying the image to both layers

When a real image loads successfully (either primary or thumbnail):
- Both `.pack-top` (Layer 3) and `.pack-bottom` (Layer 2) show the SAME `<img>`, but each is clipped by `clip-path` to show only its portion
- The CSS fallback content is hidden via a class toggle

When both images fail:
- The `<img>` is hidden via the cascading `onerror` handler
- The CSS fallback is shown instead
- The tear animation works identically — it clips and animates the container div regardless of what's inside

### Implementation

1. Build the pack container: `<div class="pack-container">` sized and centered on screen
2. Inside it, three absolutely-positioned children stacked via z-index:
   - `.pack-inner` (z-index: 1) — the card stack visual (Layer 1)
   - `.pack-bottom` (z-index: 2) — the lower wrapper portion (Layer 2)
   - `.pack-top` (z-index: 3) — the tear-off flap (Layer 3, on top)
3. Both `.pack-bottom` and `.pack-top` contain:
   - An `<img>` element for the real pack art (same source URL in both)
   - Behind it, the CSS fallback div (shown only if image fails)
4. The pack appears centered on screen after clicking "Open Pack", replacing the button area
5. Add a "Tap to Open" prompt text below the pack (subtle, pulsing opacity)

---

## Sub-step 3B: The Tear Line (clip-path)

### Defining the jagged tear edge

The illusion of a torn package comes from an **irregular, jagged line** across the top ~15% of the pack. This is achieved with `clip-path: polygon()`.

Define a set of jagged points that cross the pack horizontally at roughly 12–18% from the top. The points zigzag up and down slightly to simulate a torn edge:

```css
/* ---- CSS Variables for tuning ---- */
--tear-line-y: 15%;           /* roughly where the tear crosses */
--tear-jag-amount: 3%;        /* how far up/down the jaggies go */
--tear-anim-duration: 0.8s;   /* total tear animation time */
--tear-flap-travel: -120%;    /* how far the flap slides upward */
--tear-bottom-travel: 110%;   /* how far the bottom slides down */
```

Example tear line points (x%, y%) zigzagging across the pack:

```
0%,13%  →  8%,17%  →  15%,14%  →  22%,18%  →  30%,13%  →  
38%,16%  →  45%,12%  →  55%,17%  →  63%,14%  →  70%,18%  →  
78%,13%  →  85%,16%  →  92%,14%  →  100%,16%
```

These same points are used in BOTH layers to create a matching edge:

**Layer 3 — `.pack-top` (top flap)** — clipped to show only ABOVE the tear line:
```css
.pack-top {
  clip-path: polygon(
    0% 0%,        /* top-left corner */
    100% 0%,      /* top-right corner */
    100% 16%,     /* right side at tear line */
    92% 14%, 85% 16%, 78% 13%, 70% 18%, 63% 14%, 
    55% 17%, 45% 12%, 38% 16%, 30% 13%, 22% 18%, 
    15% 14%, 8% 17%, 0% 13%   /* tear line points, right to left, ending at left edge */
  );
}
```

**Layer 2 — `.pack-bottom` (bottom wrap)** — clipped to show only BELOW the tear line:
```css
.pack-bottom {
  clip-path: polygon(
    0% 13%,       /* left side at tear line */
    8% 17%, 15% 14%, 22% 18%, 30% 13%, 38% 16%, 
    45% 12%, 55% 17%, 63% 14%, 70% 18%, 78% 13%, 
    85% 16%, 92% 14%, 100% 16%,  /* tear line points, left to right */
    100% 100%,    /* bottom-right corner */
    0% 100%       /* bottom-left corner */
  );
}
```

**CRITICAL**: Both polygons MUST use the EXACT same tear-line points so the edges match perfectly when assembled. They share the same jagged line — top flap shows everything above, bottom wrap shows everything below.

### Initial state (before tear)

Before the user taps, both layers are in their natural position (`transform: none`). The tear line is invisible because the two pieces fit together seamlessly — it looks like one solid pack.

---

## Sub-step 3C: Tear Animation

### On tap/click, the pack tears open

When the user taps the pack, add the class `.tearing` to `.pack-container`. This triggers the following CSS keyframe animations:

**1. Flap (Layer 3) — the torn top piece:**
- Slides upward and tilts slightly as it leaves
- Fades out
- Timing: ~0.6s ease-out, starts immediately

**2. Bottom wrap (Layer 2) — the remaining wrapper:**
- Slides downward off screen
- Fades out
- Timing: ~0.8s ease-in, starts after ~0.15s delay (the flap rips first, then the wrapper falls away)

**3. Card stack (Layer 1) — revealed underneath:**
- Starts hidden: `opacity: 0; transform: scale(0.95)`
- Animates to: `opacity: 1; transform: scale(1)` — a subtle "emerge" effect
- Timing: ~0.5s ease-out, starts at ~0.3s (mid-tear, so cards appear as wrapper separates)

**4. Bonus "snap" feel:**
- The entire `.pack-container` does a quick `transform: scale(1.03)` then back to `scale(1)` in the first ~0.15s — a subtle pop when the seal breaks

### CSS implementation

```css
.pack-container.tearing .pack-top {
  animation: tearFlap var(--tear-anim-duration) ease-out forwards;
}

.pack-container.tearing .pack-bottom {
  animation: tearBottom var(--tear-anim-duration) ease-in forwards;
  animation-delay: 0.15s;
}

.pack-container.tearing .pack-inner {
  animation: revealCards 0.5s ease-out forwards;
  animation-delay: 0.3s;
}

@keyframes tearFlap {
  0%   { transform: translateY(0) rotate(0deg); opacity: 1; }
  100% { transform: translateY(var(--tear-flap-travel)) rotate(-5deg); opacity: 0; }
}

@keyframes tearBottom {
  0%   { transform: translateY(0); opacity: 1; }
  100% { transform: translateY(var(--tear-bottom-travel)); opacity: 0; }
}

@keyframes revealCards {
  0%   { opacity: 0; transform: scale(0.95); }
  100% { opacity: 1; transform: scale(1); }
}
```

Listen for `animationend` on `.pack-bottom` (the last to complete) to trigger the transition to the card reveal state (Step 4).

### The "card stack" inner visual (Layer 1)

This is what shows during and after the tear — the "inside" of the pack:

- 3–4 slightly offset rectangles using the **card-back design from Experiment 1** (dark red, crosshatch pattern, "ONE PIECE CARD GAME")
- Each offset ~2px down and ~1px right to create a stack illusion
- The top card is the first card the user will flip in Step 4

After the tear completes, this transitions into the Step 4 card reveal sequence.

---

## Sub-step 3D: Prompt Text & Interaction Cues

1. **"Tap to Open" text** below the pack:
   - Subtle, light grey text
   - Pulsing opacity animation (0.4 → 1 → 0.4, ~2s cycle) to draw attention
   - Disappears immediately when the tear starts

2. **Cursor hint** on desktop: `cursor: pointer` on the pack container

3. **Touch feedback**: on `pointerdown`, briefly dim the pack (opacity: 0.9) for tactile feedback before the tear triggers on `pointerup`/`click`

4. **Prevent double-trigger**: once the tear starts, ignore further taps. Use a `packOpened` state flag.

---

## Sub-step 3E: Set-Specific Pack Colors (CSS fallback)

When the CSS fallback is active (image didn't load), the pack gradient should change based on the selected set. Define a color map:

```javascript
const SET_COLORS = {
  'OP-01': { primary: '#c41e2a', secondary: '#8b0000' },  // Red
  'OP-02': { primary: '#1a5276', secondary: '#0c2d48' },  // Blue
  'OP-03': { primary: '#1e8449', secondary: '#0b5345' },  // Green
  'OP-04': { primary: '#6c3483', secondary: '#3b1a5e' },  // Purple
  'OP-05': { primary: '#c0392b', secondary: '#922b21' },  // Red
  'OP-06': { primary: '#2e86c1', secondary: '#1a5276' },  // Blue
  'OP-07': { primary: '#d4ac0d', secondary: '#7d6608' },  // Gold
  'OP-08': { primary: '#c0392b', secondary: '#1a5276' },  // Red/Blue
  'OP-09': { primary: '#b7950b', secondary: '#7d6608' },  // Gold
  'OP-10': { primary: '#8e44ad', secondary: '#6c3483' },  // Purple
  'OP-11': { primary: '#e67e22', secondary: '#a04000' },  // Orange
  'OP-12': { primary: '#1abc9c', secondary: '#0e6655' },  // Teal
  'OP-13': { primary: '#c0392b', secondary: '#641e16' },  // Red
  'OP-14': { primary: '#2874a6', secondary: '#1a5276' },  // Blue
};
```

Apply via CSS custom properties on the pack container: `style="--pack-primary: #c41e2a; --pack-secondary: #8b0000;"`.

The CSS fallback background uses: `background: linear-gradient(160deg, var(--pack-primary), var(--pack-secondary))`.

---

## Delete old Step 3 code

**Remove entirely** whatever CSS-drawn pack and center-split animation currently exists from the previous Step 3 attempt. Start fresh. The text list from Step 2 can remain (hide it when the pack visual appears, or keep it in the console only).

---

## Checkpoint 3 (Revised)

- [ ] Pack visual appears centered and looks like a real booster pack (real image loads OR polished CSS fallback displays)
- [ ] Pack image loads for at least OP-14 (test it). If image fails for a set, CSS fallback looks good.
- [ ] Tapping the pack plays the tear-from-top animation — NOT a center split
- [ ] Top flap tears upward with jagged edge, bottom wrapper slides down
- [ ] Card stack is revealed underneath as the wrapper separates
- [ ] Animation is smooth (~0.8s total) and satisfying
- [ ] "Tap to Open" prompt is visible before tear, disappears after
- [ ] Double-tap doesn't break anything
- [ ] After tearing, the area is clean and ready for card reveals (Step 4)
- [ ] Changing sets updates the pack visual (new image or fallback colors)
- **STOP here. Show the result. Wait for approval.**

---

## Notes for Claude Code

- **The OPTCG API does NOT have pack wrapper images.** Only the official site does. Don't waste time looking for them in the API response.
- **OP-14 set_id mismatch:** The API returns `OP14-EB04` as the set_id for The Azure Sea's Seven, not `OP-14`. Make sure the `PACK_IMAGE_MAP` lookup handles this — the key should match whatever set_id the API returns. You may need to normalize (e.g. map `OP14-EB04` → the OP-14 entry in the image map).
- `clip-path: polygon()` is well-supported in all modern browsers. We're NOT animating the polygon points — we use it as a static mask and animate `transform` + `opacity` on the masked elements. This is performant and reliable.
- Add `will-change: transform, opacity` on `.pack-top` and `.pack-bottom` for GPU compositing.
- The official site images are `.webp` format (primary) and `.png` (thumbnails). All modern browsers support both.
- The official site images may be blocked by CORS or hotlink protection in some environments. The cascading `onerror` handler (primary → thumbnail → CSS fallback) is the safety net — always implement all three levels.
- Do NOT use `<canvas>` for any of this. Pure CSS/HTML/JS only.
- Keep all animation tunables in CSS custom properties at the top of the `<style>` block.
- When the user opens another pack (later steps), the pack visual must be reconstructable — remove the `.tearing` class, reset transforms, and rebuild the layers fresh.
