# UKL Planner — Local Apartment Floor Plan & Furniture Catalog Tool

This file exists so a person (or an AI assistant) can understand this project
quickly, without archaeology. Read this first, then open `index.html` for the
actual implementation.

## What this is

A single-file, no-build, client-side web app for laying out furniture on a
real, to-scale floor plan, with an editable/shoppable furniture catalog next
to it. It runs entirely in the browser — no server, no login, no backend.
Originally built for one specific 750 sq ft / 1BR apartment, but the catalog
and tooling are generic.

Core features:
- Real floor plan (traced from the actual architectural SVG) with draggable,
  resizable, rotatable, recolorable furniture pieces
- A fully editable furniture catalog: rename, resize, retag, reprice, relink,
  and attach a photo to any entry; add brand-new entries from scratch
- Multi-select room tags per catalog item (Living, Bedroom, Kitchen, etc.),
  user-extensible — add new tags on the fly
- Unit switch (US inches / metric cm) that updates every measurement at once,
  including CAD-style dimension-line callouts drawn on the plan
- Named, saved plans (Save / Save As New / Load / Delete) so you can keep
  multiple layout variations without losing any of them
- Two-tier local persistence: silent `localStorage` autosave everywhere, plus
  an optional "Connect folder" upgrade (Chrome/Edge only) that writes real
  `data.json` + image files to a folder you choose on your computer

## How to run it

- **Just open it:** double-click `index.html`, or open it via a `file://`
  path in any modern browser. No install, no build step, no dependencies.
- **Host it as a static site:** GitHub Pages, Netlify, Vercel, or any static
  host will work — it's one self-contained HTML file with inline CSS and JS.
  Push this repo, point GitHub Pages at the root, done.
- **For real local-file saving:** use Chrome or Edge on desktop and click
  "Connect folder" inside the app. This uses the File System Access API,
  which is not supported in Safari or Firefox, and may be blocked inside a
  sandboxed iframe (e.g. an in-chat AI preview) — open the file directly in
  a normal browser tab/window for full functionality.

## Repo / folder structure

```
index.html        the entire application — markup, CSS, and JS in one file
context.md        this file
backup/           dated snapshots of previous working index.html versions,
                   kept so a bad edit can be rolled back without digging
                   through git history
assets/           NOT used by the repo itself — this is where the *running
                   app* writes uploaded furniture photos once you connect a
                   project folder. Safe to .gitignore if you don't want your
                   personal photos committed.
data.json         NOT included here — the app creates this itself the first
                   time you connect a folder. Holds your saved plans, catalog
                   edits, and project name/address. Personal data — consider
                   .gitignore-ing this too if the repo is public, or keep it
                   private/local only.
```

## How it's built (for anyone extending it)

- Vanilla HTML/CSS/JS. No frameworks, no bundler, no package.json. The only
  outbound network requests are the "Shop" links on catalog cards (real
  product URLs, or a Google Shopping search fallback for generic items) —
  the app itself never phones home.
- The floor plan background is the real architectural SVG for the unit,
  embedded inline. Room-name and dimension text are wrapped in toggleable
  `<g class="tag-room">` / `<g class="tag-dim">` groups so they can be shown
  or hidden from the UI without touching the SVG.
- All furniture math happens in "plan units" matching the source SVG's
  native coordinate space: `UPF = 10.756` (plan units per foot). Everything
  — drag positions, sizes, dimension lines — is computed from that one
  constant, so on-canvas placement stays perfectly to scale.
- State model (all in top-level `let` variables in the `<script>`):
  - `project` — editable name/address shown in the header
  - `items` — pieces currently placed on the floor plan
  - `catalog` — the shared, editable furniture library (tags, price, URL,
    photo, notes)
  - `TAGS` — user-extensible list of room tags used for filtering
  - `plans` — named snapshots of `items` + `project`, for Save/Load
  - `unitMode` — `'in'` or `'cm'`, drives every measurement display
- Persistence: `doSave()` always writes to `localStorage` first (silent,
  universal), then additionally writes `data.json` (and `assets/*.png` etc.
  for uploaded photos) to a connected folder via the File System Access API
  if one is connected. `applyState()` / `exportState()` are the single
  source of truth for what gets saved and restored — touch those carefully.
- The drag engine is plain pointer events (`onDragStart` / `onDragMove` /
  `onDragEnd`) on SVG `<g>` elements, converting client pixel deltas to plan
  units via the live bounding rect of `#furnLayer`. No drag library.

## Known limitations

- No backend, no auth, no database — intentionally personal and local-first.
- "Connect folder" only works in Chromium-based desktop browsers.
- Catalog photos are real files only in folder mode; without a connected
  folder they're stored as base64 data URLs inside `localStorage`/backups,
  which will bloat quickly with many or large images.
- The floor plan is specific to one apartment's real dimensions. Swapping in
  a different unit means re-tracing the wall SVG and recalculating `UPF`.

## If you're an AI assistant reading this

The whole app is `index.html` — read that file directly before making any
change. The two areas most likely to break silently if refactored carelessly
are the render/drag engine (`render()`, `onDragStart/Move/End`) and the
persistence layer (`doSave`, `applyState`, `exportState`) — both have several
functions elsewhere in the file that depend on their exact shape. Before
editing, copy the current `index.html` into `backup/` with today's date, the
same way earlier versions were preserved there.
