# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Asteroids clone in plain HTML5 Canvas + vanilla JS. No dependencies, no bundler, no build, no tests, no linter. UI text and code comments are in Spanish; keep that convention.

## Run

Open `index.html` in a browser, or serve locally: `npx serve .` (http://localhost:3000).

## Architecture

All logic lives in `game.js` (single script, loaded by `index.html`, canvas fixed at 800x600 via `W`/`H` constants).

- **Loop**: `loop(ts)` → `update(dt)` → `draw()` via `requestAnimationFrame`. `dt` is in seconds and clamped to 0.05.
- **State machine**: global `state` is `'playing' | 'dead' | 'gameover'`. `dead` is a 2s respawn timer (`deadTimer`) where asteroids and particles keep updating; `gameover` waits for Space to call `initGame()`.
- **Entities**: classes `Ship`, `Bullet`, `Asteroid`, `Particle`. Each has `update(dt)` / `draw()`. Removal is by `dead` flag, then arrays are re-filtered in `update`. Game state (`ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`) is module-level `let` globals, reset in `initGame()`.
- **World is toroidal**: positions use `wrap()`. Particles do not wrap.
- **Input**: `keys` (held) and `justPressed` (edge) maps keyed by `e.code`. `pressed(code)` consumes the edge, so call it once per frame per key (Space shooting and restart use it).
- **Asteroids**: size 1/2/3 indexes parallel arrays `RADII`, `SPEEDS`, `POINTS`. Note `POINTS` is inverted (smaller = more points). `split()` spawns two of `size - 1`.
- **Collisions**: circle-based via `dist()`. Ship vs asteroid uses `a.radius * 0.82` for forgiveness; ship is skipped while `invincible > 0`.
- **Levels**: clearing all asteroids calls `nextLevel()` (spawns `3 + level`, resets ship and clears bullets).

## Notes

README mentions power-ups and a shooting star; those were removed in commit 13e713f and no longer exist in code.
