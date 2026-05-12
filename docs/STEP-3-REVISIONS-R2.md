# Step 3 Revisions — Round 2

Apply these fixes to the current Step 3 implementation. Do them all in one pass, then STOP and show the result.

---

## 1. More Tilt Range

The pack tilt is too subtle — it doesn't feel interactive enough.

- Increase max tilt from ±10 degrees to **±15 degrees**
- The pack is a physical object but it should still feel responsive and fun to play with
- Keep the smooth return to flat on mouse leave

---

## 2. Glossy White Shine (Not Rainbow Foil)

The current shine effect looks too much like the rainbow holo shimmer from the cards. The pack wrapper should have a **glossy plastic shine** — like light reflecting off sealed packaging.

**Replace the rainbow/multi-color gradient with a white gloss effect:**
- Use a single diagonal band of `rgba(255, 255, 255, 0.25)` to `transparent` — a clean white highlight streak
- The band should be narrow (~20-30% of the pack width) and angled (~135deg or so)
- As the mouse moves / pack tilts, the white highlight slides across the surface — like turning a sealed pack under a light
- It should feel like a specular reflection on glossy plastic, NOT a holographic rainbow
- Increase the intensity slightly — the shine should be clearly visible, not barely-there
- On mouse leave, the shine returns to a neutral center position

---

## 3. Animated Cut — Left to Right, Not Instant

The tear/cut currently happens all at once, which feels wrong. A real cut moves across the pack.

**Animate the cut line sweeping from left to right:**

Instead of both pieces separating simultaneously, the cut should visually travel across the top of the pack from left to right before the pieces separate. Here's one approach:

### Option A: Animated clip-path reveal
- Use a **vertical clip mask** that sweeps left to right across the tear line area
- Think of it as a "cutting blade" that travels horizontally:
  1. At frame 0: the pack is whole (no visible cut)
  2. Over ~0.4s: a thin dark line or gap sweeps from left (0%) to right (100%) across the tear line position (~15% from top)
  3. As the line passes, the area behind it shows the slight gap between the top flap and bottom wrapper
  4. Once the cut reaches the right edge: THEN the flap lifts up and the wrapper slides down (the existing tear animation)

### Option B: Staggered separation
- Instead of both pieces moving at once, have the **left side of the flap start lifting first**, with the separation rippling across to the right
- This could be done by briefly animating the `clip-path` points — the left-side tear line points start moving upward slightly before the right-side points do
- Then after a brief delay (~0.2s), the full flap lifts away as before

### Implementation notes
- Option A is cleaner and easier to implement — a `::before` pseudo-element with a dark line that translates from `left: -100%` to `left: 100%` over ~0.4s, followed by the existing tear animations
- The total animation time may increase slightly (~0.4s for the cut sweep + ~0.6s for the separation = ~1s total). That's fine — it should feel deliberate, not rushed
- The cut sweep should have an easing that starts slow, speeds up in the middle, and slows at the end — like scissors closing through packaging

---

## After applying these fixes

Show the result. Verify:
- [ ] Pack tilts more noticeably (±15 degrees) and feels responsive
- [ ] Shine is a glossy white highlight, NOT a rainbow — looks like light on plastic packaging
- [ ] Shine is clearly visible and slides across the pack as you tilt
- [ ] The cut animates from left to right before the pieces separate
- [ ] After the cut completes, the flap lifts and wrapper drops as before
- [ ] The full sequence feels like: tilt the pack → tap → cut sweeps across → pieces separate → cards revealed
- [ ] Everything else from previous checkpoints still works

**STOP here. Show the result. Wait for approval.**
