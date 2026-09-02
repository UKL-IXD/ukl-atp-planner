# Changelog

All notable changes to the UKL Apartment Planner tool, newest first.
Corresponding dated backups live in `/backup`.

## v48 — Deselecting was never actually possible
- Once you selected a piece (including just by dragging it), there was
  no way to dismiss the editor/quick-action toolbar — no tap-empty-space,
  no close button, nothing. It stayed until you selected something else.
  Added both: tapping empty floor-plan space now deselects, and the
  mobile quick-action bar got an explicit × button. Caught a real bug
  building the fix — a real drag doesn't reliably fire a native "click"
  afterward, so a naive "skip the very next click" guard actually ate
  the *following* unrelated tap instead of the drag's own; switched to
  a short time-based guard instead.

## v47 — Real brand orange from ukldesign.ca
- Sampled the actual pixel color from a screenshot of the "Product
  Designer." text on your site (RGB 236,108,44 — dominant by a wide
  margin over anti-aliased edge pixels) rather than guessing. Swapped
  into the core --rust CSS variable, the theme-color meta tag, the
  color-swatch picker, and regenerated the app icon backgrounds to
  match. Left furniture-piece colors alone (e.g. the DYVLINGE chair,
  which is orange because that's the real product's color, not a
  brand-color coincidence).

## v46 — Gentler catalog/placed-item sync jumps
- Clicking a piece on the floor plan (or a catalog card's "Placed" badge)
  used to force a hard smooth-scroll to dead-center every time, even when
  the target was already fully on screen, and combined with a filter
  reset + full grid rebuild happening in the same instant. Now: skips
  the scroll entirely if the target is already visible, and when a
  scroll IS needed, moves the minimum distance instead of always
  recentering. Also respects `prefers-reduced-motion` (instant jump
  instead of animated scroll for anyone with that OS setting on), and
  softened the flash-highlight intensity slightly.

## v45 — Clean app icon
- The old icon was rasterized from hand-drawn stroke lines in Python,
  which never renders crisp at icon sizes. Replaced with a proper solid
  filled house-silhouette glyph (roof + walls + door cutout), rendered
  through an actual SVG renderer at high resolution and downsampled with
  quality filtering for each size — apple-touch-icon, 192/512 for the
  manifest, and new 16/32px favicons (didn't have any before). Also
  swapped the topbar's inline logo mark to the same glyph so the browser
  tab, home-screen icon, and in-app logo all match.

## v44 — Mobile quick-action bar
- The Rotate/Copy/Delete controls only lived in the sidebar "Edit Piece"
  panel, which sits well below the floor plan and catalog on a narrow
  screen — meant scrolling all the way down every time after selecting
  a piece. Added a fixed bottom toolbar (mobile/tablet widths only —
  desktop's sidebar is already immediately visible there) that shows
  Rotate/Copy/Edit/Delete for whatever's currently selected, regardless
  of scroll position. "Edit" jumps to and focuses the full editor for
  renaming/resizing/color, which still needs more room than a toolbar
  can give it.

## v43 — IndexedDB storage
- Replaced localStorage with IndexedDB as the primary persistence layer.
  localStorage caps out around 5–10MB, which was the real blocker for saving
  a full photo catalog on iPhone (Safari doesn't support the "Connect Folder"
  File System Access API at all, so IndexedDB is the realistic path to
  reliable on-device storage there). Existing localStorage saves migrate
  over automatically on next load, then get cleared so there's only one
  copy of (possibly large) image data. Tested with a ~12MB synthetic
  payload — well past what localStorage could hold — saving and surviving
  a reload intact.

## v42 — Touch drag no longer scrolls the page
- `touch-action:none` was only set on the outer wrapper div, not the SVG
  canvas or individual pieces; added it at every level plus explicit
  `preventDefault()` in the drag handler.

## v41 — Dark-mode override (belt-and-suspenders)
- Added `@media (prefers-color-scheme: dark)` override forcing the light
  background, in case something outside the page's own control (e.g. an
  in-app previewer) applies its own dark pass on top.

## v40 — PWA installable
- App icon (matches the topbar logo mark), `manifest.json`, and Apple
  meta tags so "Add to Home Screen" launches full-screen instead of just
  bookmarking.

## v39 — Force light color-scheme
- `color-scheme: light` (meta + CSS) to stop Safari's automatic dark-mode
  inversion of the page background/grid.

## v38 — Safari background fix
- The body background used an invalid CSS shorthand (a bare color as its
  own comma-separated layer). Chromium tolerated it; Safari's stricter
  parser threw out the whole declaration. Rewrote using explicit
  background-color/image/size/position properties.

## v37 — iPhone-friendly pass
- Bumped all input font-sizes to 16px on narrow screens (iOS Safari
  auto-zooms the page on any input under that). Verified real touch-drag
  works via CDP-level touch dispatch. Added safe-area-inset handling for
  the lightbox close button.

## v36 — Catalog merge-on-load fix
- Returning sessions were restoring a saved catalog snapshot wholesale,
  silently dropping any presets added to the code since that save
  happened. Now merges new DEFAULT_CATALOG entries in by key, with fresh
  IDs to avoid collisions.

## v35 — L-shaped piece support
- Full L-shape geometry (not just a rectangle with a label): 4-corner
  orientation picker, proper area-weighted centroid for label placement
  (the bbox center sits right on the notch corner, which is wrong).
  Two ready-made presets: L-Shaped Sectional Sofa, L-Shaped Desk.

## v33 — Round shape support generalized
- Catalog items now carry a real `round` field (previously guessed from
  the icon name, which only worked by accident for one preset).
  Exposed as a checkbox in the catalog edit form.

## v23–v34 — Real Wayfair/IKEA catalog items
- Added real product data (dimensions, pricing, ratings, shop links)
  pulled from product pages for ~10 items: dressers, coffee tables,
  record stands, a bar stool.

## v18–v22 — Legibility pass
- Image lightbox (double-click to enlarge). Long/rotated piece labels
  with auto-truncation. Multiple iterations to get label font size and
  halo thickness to scale correctly with actual rendered pixel size
  rather than abstract SVG units.

## v14–v17 — Grid, snap, quick-add
- Blueprint grid rebuilt as a true SVG pattern anchored to the building's
  real wall coordinates (was a CSS trick anchored to the canvas corner).
  Independent snap-to-grid toggle. "+ Add Item" for placing a piece
  directly without going through the catalog first.

## v10–v13 — Room dimension polish, input bug fix
- Smaller room label text with metric conversion shown underneath.
  Fixed a bug where typing into the width/depth fields fought the user
  mid-keystroke (re-render was resetting the field to a clamped value on
  every character).

## v7–v9 — Reference board
- New tab: Pinterest-style pinboard for uploading inspiration images,
  with a picker to pull any pinned image straight into a catalog item's
  photo.

## v1–v6 — Foundation
- Initial floor plan + catalog + placed-items tool, catalog CRUD,
  save/load named plans, folder connection for real file persistence,
  unit switching (in/cm).
