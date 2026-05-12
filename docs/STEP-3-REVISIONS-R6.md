# Step 3 Revisions — Round 6

Apply these fixes to the current Step 3 implementation, then STOP and show the result.

---

## 1. Remove the CSS Border Line

There is a visible blue/teal border outline around the pack container. Find and remove it.

Check ALL of these elements for `border`, `outline`, or `box-shadow` and remove them:
- `.pack-container`
- `.pack-top`
- `.pack-bottom`
- `.pack-inner`
- The perspective wrapper (whatever parent has `perspective: 800px`)
- Any other parent/wrapper div around the pack

Set `border: none; outline: none; box-shadow: none;` on all of them to be safe.

---

## 2. Crop Out the Built-In Image Padding

The `img_item01.webp` product shots are **square images (~670x670)** with the booster pack centered and transparent/empty space around it. `object-fit: cover` with a 2:3 container crops top and bottom but still leaves horizontal padding — the pack artwork doesn't fill the container edge to edge.

**Fix: oversize the image to crop past the built-in padding.**

Instead of relying on `object-fit: cover` alone, scale the image larger than the container so the actual pack artwork (not the surrounding padding) fills the entire container:

```css
.pack-top img, .pack-bottom img {
  position: absolute;
  width: 140%;
  height: 140%;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  object-fit: cover;
}
```

- The `140%` value may need tuning — the goal is that the pack artwork edges align exactly with the container edges, with zero gap or visible padding
- Start at 140% and adjust up or down until it looks right
- Different sets may have slightly different amounts of padding in their product shots — optimize for the majority and accept minor variation
- Make sure `.pack-top` and `.pack-bottom` both have `overflow: hidden` so the oversized image doesn't bleed outside

---

## After applying these fixes

Show the result. Verify:
- [ ] ZERO visible border, outline, or colored line around the pack
- [ ] Pack artwork fills the container edge to edge — no transparent padding gaps
- [ ] The pack looks like a physical object sitting on the dark background, not a framed image
- [ ] Tilt, shine, cut sweep, and tear all still work
- [ ] Test with at least 2 different sets to make sure the image scaling works across sets
- [ ] Everything from previous checkpoints still works

**STOP here. Show the result. Wait for approval.**
