# Step 4 Revisions — Round 2: Smooth Card Scale-Up After Pack Opens

Apply this fix to the current Step 4 implementation, then STOP and show the result.

---

## The Problem

After the pack wrapper slides off and the cards are revealed, the card stack **jumps harshly** from its small "inside the pack" size to the larger "viewing" size. This transition needs to be smooth.

**This is NOT about the Next Card button.** Do not touch the card-to-card navigation transitions. This is specifically about the moment after the pack opens — the cards going from pack scale to viewing scale.

---

## The Sequence That Should Happen

1. Pack tears open — wrapper slides down and away ✅ (already working)
2. Cards are visible at their small "pack size" — sitting exactly where they were inside the pack ✅ (already working)
3. **SMOOTHLY over ~0.5s**, the card stack scales up and settles into the larger viewing size (~280-320px wide). This should feel like gently pulling the cards closer to look at them.
4. Card info builds in below after the scale-up completes

Step 3 is the problem — it's currently an instant jump.

---

## The Fix

The `.card-stack` needs a CSS `transition` on its `transform` property **already defined in its base styles** — before any class is added. Then, after the wrapper animation ends, add a class that changes the scale. The transition makes it animate smoothly.

### Base CSS (always present):

```css
.card-stack {
  /* ... existing position/layout styles ... */
  transform: scale(1);                    /* starts at pack size */
  transition: transform 0.5s ease-out;    /* THIS is what makes it smooth */
  transform-origin: center top;           /* scale from top-center, grows downward */
}
```

### After wrapper slides off (class added via JS):

```css
.card-stack.revealed {
  transform: scale(1.1);  /* or whatever factor = viewing size */
}
```

### JS — add the class when the wrapper animation finishes:

```javascript
// Listen on the BOTTOM WRAPPER's animation end — that's the last piece to leave
packBottom.addEventListener('animationend', () => {
  cardStack.classList.add('revealed');
  // Card info builds in AFTER the scale-up completes
  setTimeout(() => {
    showCardInfo();
  }, 500); // matches the 0.5s transition duration
});
```

### Common mistakes to avoid:

- **Don't set `transform` directly via JS** (e.g. `element.style.transform = 'scale(1.1)'`) — this bypasses CSS transitions if the transition property isn't also set inline
- **Don't rebuild or replace the `.card-stack` DOM** after the tear — if the element is recreated, it loses the transition
- **Don't add the transition and the scale in the same class** — the transition must already exist on the element BEFORE the target value changes, otherwise the browser has no "from" state to animate from
- **Don't apply this fix to the Next Card navigation** — that's a separate system and is already working

---

## After applying this fix

Show the result. Verify:
- [ ] After the pack wrapper slides off, the cards smoothly scale up over ~0.5s
- [ ] The cards grow from their pack size to viewing size — no harsh jump
- [ ] The transition feels like gently pulling the cards closer to look at them
- [ ] Card info builds in below AFTER the scale-up completes
- [ ] The Next Card button transitions are NOT affected by this change
- [ ] Everything else from the previous checkpoint still works

**STOP here. Show the result. Wait for approval.**
