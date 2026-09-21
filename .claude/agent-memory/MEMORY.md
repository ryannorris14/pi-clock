# Pi Clock - Project Memory

## What It Is
A single-file web art piece (`index.html`): a field of clock hands that secretly spells π at 3:14 AM/PM. HTML5 Canvas, vanilla JS, zero dependencies.

## Core Mechanic
- Each hand rotates at a **constant speed** (k revolutions per 12 hours)
- `angle(t) = omega * t + phase` where `omega = 2πk / 43200`
- Phase calculated so hand is at its `targetAngle` at exactly 3:14:00 (`ALIGN_SEC = 11640s`)
- **No interpolation. No cheating.** Hands naturally align due to their speeds/phases
- Alignment works at both 3:14 AM and PM (12-hour periodicity via integer k values)

## Hand Hierarchy (164 total, draw order matters)
1. **Field hands (120)** — drawn first (background). Same gold metallic color as all hands. Target angle: point toward center (cx, piCY) at alignment → starburst convergence. k=12-972. Varieties: Fleur (2) and Stiletto (3).
2. **Guardian ring (18)** — drawn second. Pivots on ellipse (rx=0.19, ry=0.165 of scale) around π. At alignment, point radially **outward**. k=18-156. Varieties: Sovereign (0) and Gothic (1).
3. **Top bar (12)** — drawn last (foreground). Pivots follow arch: `y = barYBase - archH * sin(πt)`. Target angles follow arch tangent. Serif hooks at ends (+0.18 rad). No bezier curvature. k=12-108. Varieties: Sovereign (0) and Gothic (1).
4. **Left leg (7)** — straight vertical, alternating up/down. k=14-108. Variety: Sovereign (0).
5. **Right leg (7)** — straight vertical, alternating up/down. k=16-114. Variety: Sovereign (0).

## Intricate Hand Template System
- 4 varieties, each pre-rendered to DPR-aware offscreen canvases:
  - **Sovereign (0)**: Counterweight teardrop, narrow neck, oval cutout, arrow tip
  - **Gothic (1)**: Flared base, narrow waist, pointed-arch cutout, finial tip
  - **Fleur (2)**: Counterweight tab, tapered body, diamond cutout, spear tip
  - **Stiletto (3)**: Smooth taper, circle cutout, edge engravings
- Gold brushed metallic texture on all hands:
  - Cross-width gradient (dark edge → bright highlight → dark edge) for 3D convexity
  - Fine lengthwise brush lines (seeded random, varying brightness)
  - Cutout holes via `globalCompositeOperation: 'destination-out'` with dark rims
  - Rivets with radial gradient highlights
  - Spine engravings and cross-hatch details
- Hand widths: field 5-9px, ring 9-15px, structural 7-11px
- Templates cached per hand, rebuilt on resize

## π Geometry (relative to scale = min(W,H))
- piW = 0.28 * scale, piH = 0.30 * scale
- Bar arch height: piH * 0.09
- Legs at cx ± piW * 0.27
- Leg top connects to bar at arch height for that x-position
- Visual center piCY = (barYBase - archH + legBottom) / 2

## Time System
- `virtualTimeSec = null` → real clock; `= number` → free-running
- `timeSpeed` multiplier (1x default)
- Speed scrubber buttons: 1x, 2x, 5x, 10x, 100x, 500x
- "Skip to 3:14" jumps virtualTimeSec to ALIGN_SEC - 45
- `glowFactor(sec)`: ±50s window around alignment, quadratic ramp. Controls glow/bloom/sound only, **never** hand angles.

## Visual Effects
- Background: #0a0a0a with warm radial gradient center
- Grain texture: 40 random white pixels per frame
- Bloom: radial gradient behind π when gf > 0.25
- Per-role glow at alignment: field (×9), ring (×15), structural (×22 shadowBlur)
- All hands same gold metallic color; opacity varies slightly by role

## Sound (muted by default, Web Audio API)
- Ticking: square wave bursts, interval syncs near alignment
- Drone: sawtooth 55Hz through lowpass, fades in with gf
- Chime: C major chord (C4-E4-G4-C5) sine oscillators at gf > 0.95

## UI
- Title: "π CLOCK" (Cormorant Garamond) + subtitle (JetBrains Mono), fades after 5s idle
- Countdown: "Next π: Xh XXm XXs"
- Controls: Skip button, speed scrubber, time display
- Credit: bottom-right, 20% opacity
- Sound toggle: bottom-left

## Key Design Decisions
- User rejected interpolation approach — hands must rotate at fixed mechanical rates
- User wants NO bezier curvature on hands (removed)
- User wants all hands same gold metallic color (field same as structural)
- Field hands point toward center at alignment → starburst convergence effect
- Guardian ring creates negative-space clearing effect at alignment
- Seeded random (mulberry32, seed 314159) for deterministic hand properties across resizes
- Hands are intricate shapes with cutouts/rivets/engravings, NOT simple stroked lines
