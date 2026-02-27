# Wishlist Website — Build Plan

## Step 1: Bare-bones HTML shell
Create `index.html` with a page title, a heading, and a placeholder list. Open it in your browser — you should see a heading and a static item or two. No JavaScript yet.

## Step 2: Static wishlist item with all fields
Build out one hardcoded wishlist item that includes:
- Product image
- Product name as a hyperlink
- Price
- Quantity

Verify it renders correctly in the browser before wiring up any data.

## Step 3: JavaScript data model + render loop
Replace the hardcoded HTML with a JavaScript array of item objects. Write a `render()` function that generates the list from that array. Reload the page — it should look identical to Step 2 but now driven by data.

## Step 4: Add item form
Add a form (name, URL, image URL, price, quantity) with an "Add" button. Wiring the button should push a new object into the array and call `render()`. Test by adding a new item and watching it appear.

## Step 5: Remove item
Add a "Remove" button to each item. Clicking it should splice the item from the array and re-render. Test by adding an item then deleting it.

## Step 6: Cross-out / fulfilled toggle
Add a checkbox or "Mark fulfilled" button per item. Clicking it toggles a `fulfilled` flag and re-renders the item with a strikethrough style. Test by marking an item done and un-done.

## Step 7: Inline edit
Make the price and quantity fields editable in place (click-to-edit or just `contenteditable`). Changes should update the data model. Test by changing a price.

## Step 8: Persist to localStorage
Save the array to `localStorage` on every change. On page load, read from `localStorage` (fall back to sample data). Test by adding items, refreshing the page, and verifying they survive.

## Step 9: Basic CSS styling
Add a `style.css` and link it. Style the page layout, fonts, and the wishlist cards so they look clean. No functionality — just visual polish you can eyeball.

## Step 10: Final polish + front-end validation
All validation is client-side only — no server needed:
- **Name:** cannot be blank
- **Price:** must be a positive number
- **Quantity:** must be a positive whole number
- **Product URL and Image URL:** must start with `http://` or `https://` and be a plausible link (using a URL constructor check)
- Empty-state message ("Your wishlist is empty!")
- Small UX touches (hover effects, confirmation on delete)
