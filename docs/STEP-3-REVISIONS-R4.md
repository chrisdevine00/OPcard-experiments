# Step 3 Revisions — Round 4

Apply these fixes to the current Step 3 implementation. Do them all in one pass, then STOP and show the result.

---

## 1. Kill the Dark Container Box (CRITICAL — This Is the Main Issue)

Looking at the current screenshot, there is a clearly visible **dark grey/black rectangle** surrounding the pack image. The pack image (OP-03 Pillars of Strength) is visibly smaller than this container, leaving dark padding on all sides. This completely breaks the illusion — it looks like a picture frame, not a physical pack.

**This is the #1 priority fix. Here's exactly what to do:**

1. Find the element creating the dark box. It's almost certainly `.pack-container` or one of the layer divs (`.pack-top`, `.pack-bottom`) having:
   - A `background-color` (likely dark grey or near-black)
   - Dimensions (width/height) larger than the pack image
   - Padding or a border

2. **Remove ALL background colors** from `.pack-container`, `.pack-top`, `.pack-bottom`, and any wrapper div around them. Set them to `background: transparent` or `background: none`.

3. **Size the container to match the image exactly.** The `.pack-container` width and height should be determined BY the image, not the other way around. Options:
   - Set `.pack-container` to `display: inline-block` or `width: fit-content` so it shrinks to the image size
   - OR set a fixed width (e.g. `300px`) and calculate the height from the image's natural aspect ratio once it loads
   - The `<img>` elements inside should be `width: 100%; height: 100%; object-fit: cover` with NO extra space

4. **No padding, no margin, no border** on the pack container or its layer children. Zero gap between the container edge and the image edge.

5. After fixing, the ONLY visible thing should be the pack artwork itself — no dark frame, no background peeking through anywhere.

---

## 2. Shine — Radial/Ripple Instead of Linear Band

The current shine is a linear diagonal band. It should be more like a **radial light reflection** — the way a point light source reflects off glossy plastic. Think of the soft circular highlight you see when you hold a sealed pack under a lamp.

**Replace the linear gradient shine with a radial gradient:**
- Use `radial-gradient(circle at var(--shine-x) var(--shine-y), rgba(255,255,255,0.20) 0%, rgba(255,255,255,0.05) 40%, transparent 70%)`
- `--shine-x` and `--shine-y` are CSS custom properties updated by the mouse position / tilt values
- The highlight is a soft, circular glow that follows the cursor / tilt angle
- As the user moves the mouse, the bright spot slides across the pack surface — like moving a flashlight beam over glossy packaging
- The glow should be fairly large (covering maybe 40-50% of the pack at its brightest point) and fade gradually to transparent at the edges
- Keep the fine line micro-texture overlay from the previous revision if it's there — the radial glow on top of subtle texture lines will look great together
- On mouse leave, animate the glow back to center position

---

## 3. Z-Axis Thickness — May Already Be There

The Z-axis depth may already be implemented but invisible because of the dark container box hiding it. Once fix #1 removes the dark background, check if the thickness is visible when tilting. If it's still not visible after the container fix:

- Make sure `transform-style: preserve-3d` is on the pack's perspective wrapper
- The back-face element needs `transform: translateZ(-6px)` and a visible background color (e.g. `#2a2a2a` or a dark version of the pack's dominant color)
- It should be the same width/height as the pack face
- Check that it's not hidden behind other elements — it needs to be a child of the same `preserve-3d` container

**Don't spend time on this if it becomes visible after fixing #1.**

---

## After applying these fixes

Show the result. Verify:
- [ ] **NO dark box/border/frame around the pack** — the pack image IS the entire visible element
- [ ] The pack edges are the image edges — nothing extra visible
- [ ] Shine is a soft radial glow that follows the mouse, not a linear stripe
- [ ] Shine looks like a light reflection on glossy packaging
- [ ] Z-axis thickness visible when tilting (if not, note it and we'll address separately)
- [ ] Tilt, shine, cut sweep, and tear all still work
- [ ] Everything from previous checkpoints still works

**STOP here. Show the result. Wait for approval.**
