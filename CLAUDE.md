# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Asteroids arcade clone. Pure HTML5 Canvas + vanilla ES6, single file `game.js`. No build, no dependencies, no package.json.

## Run

Open `index.html` in browser, or:
```bash
npx serve .
```
Then visit `http://localhost:3000`. No build/lint/test commands — manual play-testing in browser is the only verification.

## Architecture (game.js, single file)

- **Canvas size**: fixed `W = 800`, `H = 600`.
- **Input**: `keys{}` (held state) and `justPressed{}` (edge-triggered, via `pressed(code)`) populated by keydown/keyup listeners.
- **Toroidal space**: all moving entities wrap position via `wrap(v, max)`.
- **Entity classes**: `Bullet`, `Asteroid`, `Ship`, `Particle` — each has `update(dt)` and `draw()`, and a `dead` flag used for filtering out of arrays each frame.
- **Asteroid sizes**: size 3 (large) → splits into two size-2 (medium) on death → splits into size-1 (small). `RADII`, `SPEEDS`, `POINTS` arrays indexed by size (index 0 unused).
- **Global mutable game state**: `ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`, `state` (`'playing' | 'dead' | 'gameover'`), `deadTimer` — reset via `initGame()`, advanced via `nextLevel()`.
- **Game loop**: `loop(ts)` via `requestAnimationFrame` computes `dt` (capped at 0.05s), calls `update(dt)` then `draw()`.
- **update(dt)**: branches on `state`. Handles shooting, entity updates, bullet↔asteroid collisions (splits asteroids, awards `POINTS`, spawns explosion particles), ship↔asteroid collision (respects `ship.invincible`), and level completion (`asteroids.length === 0` → `nextLevel()`).
- **draw()**: clears canvas, draws particles/asteroids/bullets/ship in that order, then HUD (score, level, life icons), then game-over overlay if applicable.

## Conventions

- Spanish for in-game text/UI strings and code comments (section banners like `// ── Nombre ──...`); code identifiers in English.
- No frameworks/libraries — keep everything in `game.js` unless explicitly asked to split files.
