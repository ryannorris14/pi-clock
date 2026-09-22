# pi-clock

Single-file web art piece: `index.html` (HTML5 Canvas, vanilla JS, zero dependencies,
no build step). 164 clock hands rotating at fixed rates that align to spell π at 3:14.

- Location (Linux): `/home/ryan/projects/pi-clock`
- Run: `xdg-open index.html`, or `python3 -m http.server 8000` then http://localhost:8000
- Headless smoke check: `google-chrome --headless=new --virtual-time-budget=5000 --screenshot=/tmp/pi.png file://$PWD/index.html`
- Deploy: Vercel project `pi-clock` (https://pi-clock-314.vercel.app), via the Vercel GitHub integration on push to `master`.
- Design notes / memory: `.claude/agent-memory/MEMORY.md` (autoMemoryDirectory set in `.claude/settings.local.json`, gitignored).
- Keep it a single hand-written file: no framework, no bundler. See MEMORY.md "Key Design Decisions".
