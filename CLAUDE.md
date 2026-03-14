# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A retro arcade game collection built with vanilla HTML5 Canvas and JavaScript — no build tools, no dependencies, no frameworks. Each game is a fully self-contained HTML file.

## Running the Games

Open any HTML file directly in a browser. No build step or server required:
- `index.html` — Wilbur Lander (lunar lander clone)
- `asteroids.html` — Asteroids (2-player local)
- `space-invaders.html` — Space Invaders
- `tower-defense.html` — Tower Defense
- `alien-attack.html` — Alien Attack (platformer)
- `flight-sim.html` — Flight Simulator (Three.js/WebGL, requires a server due to ES module imports)

For local development with a simple HTTP server (avoids any browser file:// restrictions):
```bash
npx serve .
# or
python -m http.server 8080
```

## Architecture

Each game is a single HTML file with embedded CSS and JS, structured into clear sections marked with `// ── Category ────` comments.

### Common patterns shared across all games

**Game loop** — `requestAnimationFrame` driving `update()` + `render()` at ~60fps.

**Input** — Global `keys` object updated via `keydown`/`keyup` events; click/mousemove handlers for mouse input.

**State machine** — A `phase` string variable drives game flow: `'start'` → `'play'` → `'over'`/`'win'`/`'land'`/`'crash'`.

**Audio** — Web Audio API with lazy-loaded `AudioContext` via `ac()`. Common helpers:
- `tone(freq, duration, type, volume)` — synthesized tone
- `boom(duration, volume, endFreq)` — noise-based explosion

**Sprites** — Pixel-art encoded as 8-byte bitmask arrays, rendered by checking bit flags per row/column.

**Particles** — Pooled particle arrays for explosions and effects; each particle has position, velocity, lifetime, and color.

**Math** — `rand(n)`, `clamp`, `wrap`, `hypot` used throughout; physics uses velocity + acceleration vectors in radians.

### Per-game notes

- **Wilbur Lander** (`index.html`): Procedural terrain with interpolated 3-point collision detection for the lander footpads.
- **Asteroids** (`asteroids.html`): Polygon-based asteroids with random vertex generation; per-ship invincibility frames after respawn. 2-player local co-op.
- **Space Invaders** (`space-invaders.html`): Fixed 800×600 canvas; destructible shields stored as pixel bitmaps; march-beat audio driven by alien count.
- **Tower Defense** (`tower-defense.html`): 22×13 tile grid; fixed enemy path; 5 tower types (Cannon, Sniper, Mortar, Frost, Laser); 4 enemy types with pixel-art sprites; homing projectiles with splash damage.
- **Alien Attack** (`alien-attack.html`): 2D tile-based platformer (80×14 grid, 40px tiles). 3 enemy types with distinct AI (patrol walkers, hover-seekers, turret spitters). One-way platforms with coyote-time + jump-buffer. Dual bullet pools (`pBullets`/`aBullets`). Camera follows player horizontally only.
- **Flight Sim** (`flight-sim.html`): The outlier — uses **Three.js** (WebGL) instead of 2D canvas. Full 6-DOF flight physics (pitch/roll/yaw, thrust, lift, stall, drag). Infinite procedural terrain via chunked Perlin-noise meshes (33×33 vertex grids cached in a `chunks` Map). No phase state machine; only a `crashed` boolean. Artificial horizon HUD is CSS transform-based, not canvas-drawn.
