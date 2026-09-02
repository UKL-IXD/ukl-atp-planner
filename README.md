# UKL — Apartment Planner

A single-file, no-build-step floor plan and furniture catalog tool for a 750 sqft 1BR apartment in Sunnyvale, CA. Built for planning furniture layout, tracking a shopping catalog with real product links/pricing, and keeping a Pinterest-style reference board — all in one page, installable as a home-screen app on iPhone.

**Live app:** _add your github.io link here once Pages is on_

## What it does

- **Floor plan** — the real architectural drawing, to scale. Drag, resize, rotate, recolor, and duplicate furniture on it. Snap-to-grid, a blueprint grid overlay anchored to the real walls, and CAD-style dimension callouts on the selected piece.
- **Catalog** — a library of furniture pieces (shared across all plans), each with size, color, tags, price, a real shop link, and a photo. Fully editable: add your own, edit anything, or pull one straight from Wayfair/IKEA/etc.
- **Reference board** — a Pinterest-style pinboard tab for moodboard images. Any pinned image can be used directly as a catalog item's photo.
- **Saved plans** — name and switch between multiple layouts; the catalog is shared across all of them.
- **Units** — toggle in/cm anywhere, instantly.

## Tech notes

- **Single HTML file.** No build step, no npm, no framework. Open `index.html` and it runs.
- **Storage:** IndexedDB (works on Safari/iOS, unlike the folder-connect feature below). Everything — plans, catalog, photos, reference board — lives there automatically as you work.
- **"Connect Folder"** is an optional extra: on Chrome/Edge desktop, it can additionally write real files to a folder on disk (using the File System Access API). This API doesn't exist in Safari, so on iPhone the app just relies on IndexedDB, which is sufficient on its own.
- **"Download Backup"** exports one self-contained `.json` file with everything embedded (including images) — the real, portable backup, independent of the browser.

## Files

| File | Purpose |
|---|---|
| `index.html` | The entire app |
| `manifest.json` | Web app manifest — lets it install as a home-screen app |
| `apple-touch-icon.png`, `icon-192.png`, `icon-512.png` | App icons |
| `CHANGELOG.md` | Version history |
| `WORKFLOW.md` | How to push updates + rollback |
| `context.md` | Deeper technical notes (data model, SVG structure) — useful if you ever hand this codebase to another AI assistant or want the full internals |
| `backup/` | Dated snapshots of `index.html`, one per meaningful change |

## Updating this repo

See [`WORKFLOW.md`](./WORKFLOW.md) for the update cycle — short version: Claude hands you a new `index.html`, you re-upload it here through the GitHub web UI, GitHub Pages redeploys automatically in under a minute.

## Installing on iPhone

Open the live link in **Safari** (not an in-app browser) → Share icon → **Add to Home Screen**.
