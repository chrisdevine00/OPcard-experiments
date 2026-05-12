# Step 3 Revisions — Visual Fixes

Apply these fixes to the current Step 3 implementation. Do them all in one pass, then STOP and show the result.

---

## 1. Pack Size — Too Small

The pack needs to be significantly larger. Increase the pack container dimensions:
- **Mobile:** ~260px wide (up from ~200px)
- **Desktop:** ~300px wide
- Maintain the ~5:7 aspect ratio
- Make sure the pack still fits comfortably on screen with the "Tap to Open" text below it

---

## 2. Shiny Foil Effect + Slight Tilt Interaction

The pack should feel like a premium physical object, not a flat image.

### Foil shine overlay
- Add a diagonal gradient overlay on the pack wrapper (semi-transparent white-to-transparent band)
- This overlay should shift position based on mouse movement (same concept as the holo shimmer from Experiment 2)
- On mobile, it can be static or react to device tilt if gyroscope is available
- Keep it subtle — a gentle light reflection, not overpowering

### Tilt interaction
- Reuse the same perspective tilt logic from Experiment 1 — on mouse hover, the pack tilts slightly following the cursor
- **Max tilt: ±10 degrees** (less than the card's ±18 — the pack is a bigger object, should feel heavier)
- Smooth return to flat on mouse leave
- On mouse leave, the foil shine should also return to its default position

### 3D thickness
- Since the pack now tilts, it needs a slight sense of depth so it doesn't look paper-thin when rotated
- Add a **thin edge/side** visible when tilted using a pseudo-element or box-shadow trick:
  - A `::after` pseudo-element on the pack container, positioned to simulate a ~4-6px thick edge
  - Color: slightly darker than the pack's primary color
  - Only visible when the pack is tilted (it naturally appears due to the 3D perspective)
- Wrap the pack in a container with `perspective: 800px` and use `transform-style: preserve-3d`

### Important
- The tilt and shine must STOP when the tear animation begins — freeze the pack flat before tearing
- The tilt should not interfere with the tap/click to open

---

## 3. Tear Line — Make It a Clean Cut

The current jagged/zigzag tear line looks wrong. Real OPTCG packs are opened with scissors or a clean tear across the top, not a rough rip.

**Replace the jagged `clip-path: polygon()` points with a nearly straight line:**

Instead of zigzagging between 12-18%, use a straight horizontal cut at ~15% from the top. You can add very minimal variation (±1% at most) to keep it from looking perfectly computer-generated, but it should read as a clean cut, not a jagged tear.

Example — nearly straight tear line:
```
0%,15%  →  15%,15.5%  →  35%,14.8%  →  50%,15.2%  →  65%,14.9%  →  85%,15.3%  →  100%,15%
```

Update BOTH the `.pack-top` and `.pack-bottom` clip-path polygons to use the same updated points.

---

## 4. Torn Flap Travels Too Far

The top flap (the piece that tears off) currently flies all the way to the top of the screen. This feels unnatural — it should separate and drift away just enough to reveal the opening, then fade out.

**Reduce the flap travel distance:**
- Change `--tear-flap-travel` from `-120%` to something like `-40%` to `-50%`
- The flap should move upward roughly its own height, not fly off screen
- Keep the slight rotation (-5deg) — that still looks good
- The fade-out should complete by the time the flap reaches its final position so it dissolves away naturally rather than visibly stopping

**Also check the bottom wrapper travel** — make sure `--tear-bottom-travel` isn't excessive either. It should slide down just enough to clear the card stack area, roughly `30%-50%` downward, then fade.

---

## After applying these fixes

Show the result. Verify:
- [ ] Pack is noticeably larger and fills the space better
- [ ] Pack tilts slightly on mouse hover with a shiny foil reflection
- [ ] Pack has a subtle 3D thickness visible when tilted
- [ ] Tear line is a clean, nearly straight cut across the top
- [ ] Torn flap drifts up a short distance and fades — doesn't fly off screen
- [ ] Bottom wrapper slides down gently and fades
- [ ] Tilt stops before the tear animation plays
- [ ] Everything else from the previous checkpoint still works

**STOP here. Show the result. Wait for approval.**
