# MIGRATION.md — pi-clock (Windows 10 → Ubuntu 26.04)

Written 2026-09-21 by the pi-clock project agent during the machine wipe.
Audience: the Linux orchestrator agent rebuilding from GitHub + the user-folder backup.

## What this is

A single-file web art piece: `index.html` (1032 lines, HTML5 Canvas, vanilla JS,
**zero dependencies, no build step**). A field of 164 clock hands rotating at fixed
mechanical rates that align to spell π at 3:14 AM/PM. Full design notes are in
`.claude/agent-memory/MEMORY.md` (verbatim copy of the Claude auto-memory).

## Fresh-clone setup on Linux

```bash
git clone https://github.com/ryannorris14/pi-clock.git
cd pi-clock
# Run: just open it in a browser
xdg-open index.html
# or serve it (only needed if a browser blocks file:// features)
python3 -m http.server 8000   # then http://localhost:8000
```

That is the entire setup. Nothing to install.

Restore Claude auto-memory: copy `.claude/agent-memory/MEMORY.md` to
`~/.claude/projects/<encoded-path-of-this-repo>/memory/MEMORY.md` (Claude Code
derives the folder name from the repo's absolute path, so it will differ on Linux).
Read the "Memory staleness" section below first.

## In-flight context (not in memory or code)

- **Task state:** no work in progress. Last commit `b33d4f8` (2026-03-11,
  "Add intricate hand designs, remove sound, fix speed controls"). Working tree was
  clean; nothing half-done. No open branches, no TODOs in code.
- **Next steps:** none recorded. No known bugs recorded.
- **Open decisions:** none. Design rules Ryan has set (see MEMORY.md "Key Design
  Decisions") are the constraints for any future work: fixed-rate rotation with no
  interpolation, no bezier curvature on hands, all hands the same gold metallic
  finish, intricate hand shapes (not stroked lines), seeded random (314159).
- **Memory staleness — MEMORY.md is behind the code in three places:**
  1. MEMORY.md describes a Web Audio sound system (ticking, drone, chime, sound
     toggle bottom-left). Commit `b33d4f8` **removed all sound**; `index.html` has
     no audio code at all. `glowFactor` now drives glow/bloom only.
  2. Speed scrubber: MEMORY.md lists 1x–500x; code has 1x, 2x, 5x, 10x, 100x,
     500x, **1000x**.
  3. "Sound toggle: bottom-left" no longer exists. Credit text is "Concept by Duncan".
  Everything else in MEMORY.md was spot-checked against the code and matches
  (ALIGN_SEC, skip-to = ALIGN_SEC − 45, piW/piH 0.28/0.30, ring rx/ry 0.19/0.165,
  120 field hands, 18 ring k-values, mulberry32(314159)).
- **How Ryan wants it built:** single file, hand-written, no framework, no bundler.
  Keep it that way.

## Deployment (Vercel) — the one thing outside the repo

- Vercel project **`pi-clock`**, id `prj_rax26kxqdYZ1UCc5k0N61twsRSiN`,
  team `team_h76vKKMxmlsiVc6fYbOAhL6z` (personal team "ryan-norris-projects-e2480763").
- **Production URL: https://pi-clock-314.vercel.app** — verified 2026-09-21: HTTP 200,
  byte-identical to `index.html` at HEAD.
- Framework preset: none (static). Project node setting 24.x (irrelevant, nothing runs).
- Deployment protection: SSO enabled for "all except custom domains" — production
  `.vercel.app` alias serves publicly (curl 200); preview URLs need Vercel login.
- Latest deployment was created 2026-03-11 18:48 UTC, the same minute as the last
  commit, and a `pi-clock-git-master-…` alias exists → almost certainly deployed via
  the Vercel GitHub integration (GitHub App; it does not show up as a repo webhook).
  **Not verified from the dashboard.** If a future push does not auto-deploy, either
  reconnect the repo in Vercel → project → Settings → Git, or deploy manually:
  `npm i -g vercel && vercel login && vercel link` (pick the existing `pi-clock`
  project) `&& vercel --prod`.
- No `.vercel/` directory is tracked (project was never linked locally on Windows
  either). No `vercel.json` needed.
- **Stale:** the GitHub repo "homepage" field says `https://pi-clock-ten.vercel.app`,
  which returns 404. Should be updated to `https://pi-clock-314.vercel.app`
  (`gh repo edit ryannorris14/pi-clock --homepage https://pi-clock-314.vercel.app`).

## Git

- Remote `origin` = https://github.com/ryannorris14/pi-clock.git, branch `master`
  (the only branch). GitHub account `ryannorris14`, repo public.
- Commit identity used here: Ryan Norris <ryannorris14@gmail.com>.
- Line endings: `index.html` is LF in the index and worktree. Windows had
  `core.autocrlf=true`; not needed on Linux. No `.gitattributes` (none needed).

## Untracked / gitignored files needed to run

**None.** There is no `.gitignore`, and `git status --ignored` showed nothing.
The repo is exactly `index.html` (+ this file and `.claude/agent-memory/`).
No `.env`, no secrets, no credentials of any kind in this project.

## Environment / dependencies

- **Runtime dependencies: none.** No package.json, no lockfile — none created,
  because there is no package manager in play and adding one would be fiction.
- **Runtime:** any modern browser (Canvas 2D, `devicePixelRatio`,
  `requestAnimationFrame`). Tested on Chromium on Windows.
- **Network dependency at runtime:** Google Fonts (`Cormorant Garamond`,
  `JetBrains Mono`) via `<link>` in `<head>`. Not pinned (Google Fonts CSS2 API has
  no version pin). Offline, it falls back to the browser's serif / monospace; the
  clock itself is unaffected.
- **System-level dependencies:** none. A browser. Optionally `python3` for a
  local static server.
- **Tooling on the old Windows machine, for reference only (nothing in this repo
  needs them):** git 2.53.0.windows.1, gh 2.87.3, node 24.13.1, vercel CLI 59.10.0.

## Windows-isms

Audited `index.html` and the repo. **None found:**
- no hardcoded paths, no `C:\`, no `file://`
- no `.bat` / `.ps1` scripts, no scheduled tasks, no environment variables
- no `localStorage` or other host-specific state
- one file, lowercase name, no case-sensitivity exposure
- LF line endings already

## Linux restore log (2026-09-21, Ubuntu 26.04)

- Cloned fresh from `origin/master` (HEAD `a020be5`) into `/home/ryan/projects/pi-clock`.
- No untracked files to copy (backup folder contained only the repo). No secrets.
- Added `.claude/settings.local.json` with `autoMemoryDirectory` →
  `/home/ryan/projects/pi-clock/.claude/agent-memory` (MEMORY.md present from the clone);
  added `.gitignore` so that local settings file is never committed.
- Line endings already LF; no Windows-isms found (re-audited: no `C:\`, no scripts).
- Added a short `CLAUDE.md` with Linux run/smoke-check commands.
- Smoke check: headless Google Chrome screenshot of `index.html` renders the hand field,
  countdown, speed controls and credit correctly.
- Still outstanding (not done by the restore, remote change): GitHub homepage field is stale —
  `gh repo edit ryannorris14/pi-clock --homepage https://pi-clock-314.vercel.app`.
