# Wishlist Website — Code Review & Bug Fix Conversation

---

## User

Okay, review the code you just wrote, by checking for possible bugs, edge cases, and anything that could break. What would you change?

---

## Assistant

Here's my full review, going through CSS then JavaScript:

---

**CSS**

**1. Image height is unconstrained**
`img.width = 80` is set in JS but there's no `height` or `max-height` in CSS. A portrait-oriented product image could make a row 300–400px tall and break the layout. Fix: add `#wishlist img { height: 80px; object-fit: contain; }`.

**2. `font-family` not inherited by form inputs**
Browsers apply their own default font to `<input>` and `<button>` elements — they don't automatically inherit from `body`. The page font (`system-ui`) will look correct everywhere except inside form fields. Fix: add `font-family: inherit` to both `#add-form input` and `#add-form button[type="submit"]`.

**3. Inline price input width (55px) too narrow for large values**
"$1234.56" at `0.875rem` would overflow or be clipped in a 55px box. Should be at least 70–75px.

**4. Submit button has no `:focus` style**
Keyboard users tabbing to the submit button get no visible focus indicator — the button just looks normal. All other interactive elements have `outline: 2px solid #0055a5` on focus but the submit button was missed.

**5. Form number inputs inherit the full 320px width**
`#add-form input { max-width: 320px }` applies to the price and qty number fields too. They end up much wider than needed. A narrower `max-width` scoped to `input[type="number"]` would be cleaner.

---

**JavaScript**

**6. `loadItems()` still doesn't check `Array.isArray(parsed)` (line 253)**
Flagged in the last review, never fixed. If localStorage contains valid JSON that isn't an array (e.g. `null`, `{}`), `.map()` throws a `TypeError`. The `catch` handles it by falling back to `DEFAULT_ITEMS`, silently wiping the user's list. Fix: add `if (!Array.isArray(parsed)) return [...DEFAULT_ITEMS];` before the `.map()`.

**7. `DEFAULT_ITEMS` shallow copy still not fixed (line 268)**
`return [...DEFAULT_ITEMS]` shares object references. If the default item is ever mutated (e.g. during a future reset feature), `DEFAULT_ITEMS[0]` would be changed too. Fix: `return DEFAULT_ITEMS.map(function(item) { return { ...item }; })`.

**8. No bounds check on `items[index]` in event handlers (lines 394, 409)**
Both the delete click handler and the inline-edit change handler only check `isNaN(index)`, not whether `index` is within range. If a stale `data-index` somehow fires (e.g. a rapid double-click during a re-render), `items[index]` is `undefined` and accessing `.price` or `.quantity` throws. Fix: add `if (index < 0 || index >= items.length) return;`.

**9. Optional image URL field can silently block form submission**
`<input type="url" id="input-image">` is optional (no `required`), but if the user types a partial URL like `amazon.com/img.jpg` (missing protocol), the browser's own URL constraint validation marks it invalid and **never fires the `submit` event** — with no clear error shown, since browser tooltip placement for optional-but-invalid fields varies. Our `input` listener can't clear browser-native validity. Fix: change it to `type="text"` and rely solely on our own `isValidUrl()` check.

**10. `setCustomValidity("")` cleanup in submit handler is now redundant (lines 457–459)**
The `input` listener added in the last fix already clears custom validity the moment the user types anything. The cleanup loop at the top of the submit handler now never finds a stale custom error to clear. It's dead code — harmless but misleading.

**11. `!isNaN(item.price)` in the filter is always true after sanitization (line 262)**
The `.map()` before the `.filter()` already replaces any NaN price with `0`, so `!isNaN(item.price)` is always `true` by the time the filter runs. The intended check should be `item.price >= 0`.

---

**Summary by priority:**

| Priority | Issue |
|----------|-------|
| High | `Array.isArray` guard missing — can silently wipe saved list |
| High | No bounds check on `items[index]` — can throw on stale index |
| High | Optional `type="url"` image field silently blocks submit |
| Medium | `font-family` not inherited by inputs — inconsistent typography |
| Medium | Image height unconstrained — tall images break row layout |
| Medium | `DEFAULT_ITEMS` shallow copy — latent mutation bug |
| Medium | Submit button missing `:focus` style — keyboard accessibility gap |
| Low | Inline price input too narrow (55px) |
| Low | `setCustomValidity` cleanup loop now dead code |
| Low | `!isNaN(item.price)` filter check is always true |

Want me to apply all of these, or a subset?

---

## User

Okay, fix the following bugs mentioned. For error 1, restrict the image height so that the wishlist UI stays clean. Make the fonts consistent in between different browsers for error 2. Make sure the text stays inline properly for all of the text inputs for error. For error 6, fix the code so that the array can be stored locally on the user's computer. Error 8, make sure the list doesn't err if the user accidentally double clicks. Fix error 9 for a proper image link. Fix error 10 so that the code doesn't contain redundant code. Fix error 11 for the price. Push and commit these changes.

---

## Assistant

All 8 fixes committed and pushed. Here's a summary of what changed:

| Error | Change |
|-------|--------|
| 1 | Added `#wishlist img { width: 80px; height: 80px; object-fit: contain; }` — tall images no longer distort rows |
| 2 | Added `font-family: inherit` to `#add-form input` and `#add-form button[type="submit"]` |
| 3 | Widened inline number inputs from `55px` → `72px`; added `font-family: inherit` there too |
| 6 | Added `if (!Array.isArray(parsed)) return [...DEFAULT_ITEMS];` before `.map()` in `loadItems()` |
| 8 | Both event handlers now check `index < 0 \|\| index >= items.length` before touching `items[index]` |
| 9 | Image URL input changed from `type="url"` to `type="text"` — browser native URL validation no longer silently blocks submission |
| 10 | Removed the redundant `setCustomValidity("")` cleanup loop at the top of the submit handler |
| 11 | Filter changed from `!isNaN(item.price)` to `item.price >= 0` |
