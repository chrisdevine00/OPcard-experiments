# Step 3 Revisions — Round 5

Apply this fix to the current Step 3 implementation, then STOP and show the result.

---

## 1. Dark Border Present On Load, Disappears On Tear (The Only Fix This Round)

The dark border/background around the pack is visible **from the moment the pack loads** — even at 0° tilt with no interaction. It disappears the instant the pack is clicked to open (when the `.tearing` class is added).

This means something in the `.tearing` CSS is fixing it — either setting the container background to transparent, resizing it, or changing overflow.

### How to debug this:

1. **Compare computed styles** of `.pack-container` (and its parent/wrapper elements) before and after `.tearing` is added
2. Look specifically at these properties changing between the two states:
   - `background-color` / `background`
   - `width` / `height` / `padding` / `margin`
   - `overflow`
   - `border`
   - `box-shadow`
3. Whatever property changes when `.tearing` is added that makes the border disappear — **that's the bug**

### How to fix:

Take whatever the `.tearing` state does to eliminate the dark border and apply it to the **default (pre-tear) base CSS** so it looks correct from load.

The pack should look identical on load to how it looks the instant before the tear animation plays — no dark frame visible at any point in the lifecycle.

---

## After applying this fix

Show the result. Verify:
- [ ] Pack loads with NO dark border/frame visible — clean from the start
- [ ] The pack still looks correct during tilt interaction
- [ ] Clicking to open still works — tear animation unchanged
- [ ] Everything from previous checkpoints still works

**STOP here. Show the result. Wait for approval.**
