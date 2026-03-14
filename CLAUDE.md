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

- **Wilbur Lander**: Procedural terrain with interpolated 3-point collision detection for the lander footpads.
- **Asteroids**: Polygon-based asteroids with random vertex generation; per-ship invincibility frames after respawn.
- **Space Invaders**: Fixed 800×600 canvas; destructible shields stored as pixel bitmaps; march-beat audio driven by alien count.
- **Tower Defense**: 22×13 grid; fixed enemy path; 5 tower types (Cannon, Sniper, Mortar, Frost, Laser); 4 enemy types with pixel-art sprites; homing projectiles with splash damage.
