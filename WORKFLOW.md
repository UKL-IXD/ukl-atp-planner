# Update workflow

No build tools, no npm, no branches to manage. The whole "dev cycle" is:

1. You ask Claude for a change.
2. Claude edits `index.html`, **tests it in an actual browser before sending it** (not just reading the code), and hands you the updated file.
3. You replace the file in GitHub (steps below).
4. GitHub Pages redeploys automatically — usually live within 30–60 seconds.
5. Refresh the page (hard-refresh if you don't see the change — see below).

That's the entire loop. There's no separate "build" step because this is a static HTML file — what Claude sends you is exactly what ships.

## Replacing a file on GitHub (web UI)

1. Open your repo on github.com
2. Click into the file you're replacing (e.g. `index.html`)
3. Click the **pencil icon** (Edit) — or, for a full swap: go to the repo root, click **Add file → Upload files**, and drag the new version in. Uploading a file with the same name overwrites it.
4. Scroll down to **Commit changes**. Write a short message — see the convention below.
5. Click **Commit changes** (commit directly to `main`)

## Commit message convention

Match it to the version note Claude gives you and the `CHANGELOG.md` entry, e.g.:

```
v44 — fix catalog card overflow on narrow screens
```

This makes your GitHub history and your changelog line up, so if you're ever hunting for when something changed, both tell the same story.

## If you don't see your update

Browsers cache aggressively. Try, in order:
1. Hard refresh: **Cmd+Shift+R** (Mac) / **Ctrl+Shift+R** (Windows)
2. If installed on iPhone as a home-screen app: fully close it (swipe up/away) and reopen
3. Check the **Actions** tab on GitHub — there's a small progress indicator each time Pages redeploys; if it's still running, the update isn't live yet

## Rolling back

Every version is preserved two ways:
- **Git history itself** — click **History** on any file in GitHub to see every past version and restore one
- **`/backup` folder** — a dated, labeled copy of `index.html` for every meaningful change this session, independent of git

If something breaks, either works as a rollback; the `/backup` folder is usually faster since the filenames describe what changed.

## A few things worth knowing as a designer, not a developer

- **You will never need to run a terminal command for this project.** Everything above happens through GitHub's website.
- **There's no "staging" environment.** Whatever's on the `main` branch is what's live. For a personal tool like this, that's fine — just know there's no safety net between "I uploaded it" and "it's live for anyone with the link."
- **The repo is public** (required for free GitHub Pages on a personal account). That means the *code* is visible to anyone — not your saved plans or photos, since those live in your browser's IndexedDB, never in the repo. But don't put anything sensitive (API keys, personal info) directly in `index.html` itself.
- **Claude tests before sending, but you're the last check.** Especially anything visual — screenshots don't always catch everything on your specific device.
- **The `/backup` folder will keep growing.** That's intentional — it's your safety net. No need to clean it up.
