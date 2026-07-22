# Tally — Activity Tracker

A single-page activity logging, head-to-head ranking, and analytics dashboard
("TALLY: log · rate · rank"). Originally designed in
[claude.ai/design](https://claude.ai/design/p/3e3c0453-2066-43c5-93a5-ce4ebac10415)
(project: "Activity logging and analytics dashboard", file `Activity Tracker.dc.html`).

## How it works

This is a static two-file app:

- **`index.html`** — the app itself: markup, styles, and logic, written in
  Claude's `.dc.html` template format (`<x-dc>`, `{{ }}` bindings, `sc-for`/`sc-if`).
  This is the file you edit to change the app.
- **`support.js`** — the generic runtime that parses and executes the `.dc.html`
  template. It's a generated/vendored build artifact (React-based) — do not
  hand-edit it; treat it as a vendor file.

There's no build step. `index.html` references `./support.js` directly, so the
repo root is the deployable site as-is.

## Data persistence

All app data (log entries, custom categories, head-to-head matchup history)
is stored client-side in `localStorage`, under the `tally-*` keys:

- `tally-entries`
- `tally-customCategories`
- `tally-matchupHistory`

This means:
- **Your data survives redeploys.** Pushing new code only replaces the static
  files; it never touches `localStorage`. As long as the site is served from
  the same domain, your data stays put.
- **Data is per-browser, per-device.** It does not sync across browsers or
  devices, and clearing site data/cookies for the domain will erase it. There's
  no backend/database in this setup — if you later want cross-device sync,
  that would need a real backend (e.g. Cloudflare D1/KV + a Worker) added on
  top of this.
- If `tally-entries` is empty on first load, the app seeds some sample entries
  (see `generateSeedEntries()` in `index.html`) rather than starting blank.

## Working on it locally

No install required — it's plain static files. Preview with any static file
server, e.g.:

```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

Edit `index.html`, refresh the browser to see changes.

## Deploying (Cloudflare Pages)

This repo is meant to be connected to Cloudflare Pages via its **Git
integration**, so every `git push` to `main` auto-deploys:

1. Push this repo to GitHub (see below).
2. In the Cloudflare dashboard: **Workers & Pages → Create → Pages → Connect
   to Git**, pick this repo.
3. Build settings:
   - **Build command:** (leave empty — no build step)
   - **Build output directory:** `/`
4. Deploy. Cloudflare will give you a `*.pages.dev` URL; you can attach a
   custom domain afterward from the Pages project settings.

From then on, `git push origin main` triggers a new deploy automatically.

## Syncing with claude.ai/design

The canonical design project this was pulled from is
`3e3c0453-2066-43c5-93a5-ce4ebac10415` ("Activity logging and analytics
dashboard"). If you make edits in the Design tool and want them pulled into
this repo, or want local edits here pushed back up to the Design project, ask
Claude Code to sync it via the design MCP — just say so in a session in this
repo.
