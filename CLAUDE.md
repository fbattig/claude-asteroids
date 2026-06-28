# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

```bash
npx serve .
# visit http://localhost:3000
```

Or just open `index.html` directly in a browser — no build step, no bundler.

## Architecture

Single-file game: all logic lives in `game.js` (~424 lines). No dependencies, no modules, no build.

**Game loop:** `requestAnimationFrame` → `loop(ts)` → `update(dt)` then `draw()` every frame. `dt` capped at 50ms.

**State machine** (`state` global): `'playing'` → `'dead'` (2s respawn timer) → back to `'playing'`, or `'gameover'` when lives hit 0. Space restarts from `'gameover'`.

**Classes:**
- `Ship` — player, holds rotation/thrust/invincibility/shoot cooldown logic
- `Asteroid` — irregular polygon, sizes 1–3, `split()` produces 2× smaller on hit
- `Bullet` — 1.1s TTL, spawns from ship nose
- `Particle` — explosion fragments, fade by alpha

**Input:** `keys` dict (held) + `justPressed` dict (one-frame pulse). `pressed(code)` consumes the pulse.

**Constants to know:**
- `RADII = [0, 16, 30, 50]` — collision radius by asteroid size
- `SPEEDS = [0, 85, 55, 32]` — base speed by size
- `POINTS = [0, 100, 50, 20]` — score by size
- Canvas is fixed `800×600`, all positions wrap via `wrap(v, max)`

**Collision detection:** simple circle-circle (`dist(a,b) < a.radius + b.radius`). Ship hitbox uses `0.82` multiplier against asteroid radius for forgiving feel.
