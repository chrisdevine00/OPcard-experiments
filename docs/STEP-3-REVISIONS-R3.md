# Step 3 Revisions — Round 3

Apply these fixes to the current Step 3 implementation. Do them all in one pass, then STOP and show the result.

---

## 1. Slower Cut Animation

The left-to-right cut sweep is too fast — it needs to feel like scissors working through sealed packaging, not a quick slash.

- **Double the cut sweep duration** — from ~0.4s to ~0.8s
- Keep the easing that starts slow, accelerates through the middle, and slows at the end (`cubic-bezier(0.4, 0, 0.2, 1)` or similar)
- The separation (flap lifting, wrapper dropping) should still start only AFTER the cut sweep completes
- Total animation: ~0.8s cut + ~0.6s separation = ~1.4s. That's fine — it should feel satisfying and deliberate

---

## 2. Softer, More Realistic Shine

The current white shine is too harsh — it reads as a sharp white line drawn on the pack rather than light naturally hitting a foil surface.

**What we want:** The same kind of diffuse, soft shimmer that the card holo texture creates in Experiment 2 — but in white/silver instead of rainbow. Think of how a sealed foil pack catches light — it's a broad, soft glow that moves across the surface, not a hard-edged stripe.

**Specific changes:**
- **Wider band** — increase the shine gradient width from ~20-30% to ~50-60% of the pack. It should be a broad wash of light, not a thin line
- **Softer edges** — the gradient should fade gradually from transparent → white → transparent. No hard transitions. Use multiple gradient stops: `transparent 0%, rgba(255,255,255,0.08) 30%, rgba(255,255,255,0.18) 50%, rgba(255,255,255,0.08) 70%, transparent 100%`
- **Lower peak opacity** — max ~0.18-0.20 rather than 0.25. It should be visible but gentle
- **Consider using the fine line texture from Experiment 2** — the repeating thin-line overlay that creates the holographic micro-texture. Apply the same technique but with white/silver tones instead of rainbow. This gives the pack that "foil packaging" sheen with depth rather than a flat gradient wash
- **Keep the movement** — the shine should still shift as the user tilts the pack. It just needs to look like diffuse reflected light, not a spotlight beam

---

## 3. Remove Black Container / Border

There's a visible dark black border or container background around the pack that shouldn't be there. The pack image should be the only visible element — no frame, no background box behind it.

**Find and fix:**
- Check `.pack-container` and its children for any `background-color`, `border`, or `outline` that's creating a dark box
- If the pack container needs a background for layout purposes, make it `transparent`
- Check if the issue is the pack container being larger than the actual pack image — if so, the container should be sized to exactly match the image dimensions
- Make sure `overflow: hidden` is set on the pack layers so nothing bleeds outside the pack shape

---

## 4. Crop Pack to Exact Image Rectangle

The pack needs to be cropped tightly to the image edges. Right now the container appears to extend beyond the pack image, which breaks the 3D illusion when tilting — it looks like a flat image on a card rather than a physical pack rotating.

**Fix:**
- The `.pack-container` dimensions should exactly match the visible pack image (no padding, no extra space)
- The `<img>` inside should fill the container completely — `width: 100%; height: 100%; object-fit: cover`
- If the pack image has transparent areas or doesn't perfectly fill the rectangle, use `object-fit: cover` and `object-position: center` to crop it tight
- The CSS fallback pack should also have no extra padding or margin around it

---

## 5. Z-Axis Thickness on the Pack

When the pack tilts, it looks paper-thin. A real booster pack has ~4-5mm of thickness from the cards inside. Add depth on the Z-axis using `transform-style: preserve-3d`.

**Approach — use `translateZ` to create a layered 3D slab:**

- The pack container should have `transform-style: preserve-3d`
- Create a side/edge element using a `::before` or `::after` pseudo-element (or a dedicated div):
  - Position it behind the main pack face using `transform: translateZ(-6px)` (or similar small value)
  - Give it the same width/height as the pack
  - Background: slightly darker version of the pack's dominant color (or a neutral dark grey like `#1a1a1a`)
  - This creates a visible "slab" edge when the pack is rotated
- Alternatively, create **four edge strips** (top, bottom, left, right) that are thin (6px) and positioned on the sides of the pack using rotateX/rotateY transforms. This gives a box-like appearance from any tilt angle.
- The simpler version: just a single back face with `translateZ(-6px)` — this alone creates a noticeable sense of depth when the pack tilts because you can see the gap between the face and the back
- Make sure the edge/back elements are BEHIND the pack face (lower z-index or `translateZ` with negative value)
- These thickness elements should be hidden or removed when the tear animation starts

---

## After applying these fixes

Show the result. Verify:
- [ ] Cut sweep is noticeably slower (~0.8s) and feels like scissors cutting
- [ ] Shine is a soft, diffuse glow — not a harsh white line
- [ ] Shine looks like light on foil packaging (broad, gentle, with texture if possible)
- [ ] No dark border or background visible around the pack
- [ ] Pack is cropped tightly to the image — no extra space around it
- [ ] When tilting, the pack has visible Z-axis thickness — looks like a physical object, not a flat image
- [ ] Tilt, shine, cut, and tear all still work together smoothly
- [ ] Everything from previous checkpoints still works

**STOP here. Show the result. Wait for approval.**
