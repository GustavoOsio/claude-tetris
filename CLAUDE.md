# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

A dependency-free Tetris clone in vanilla JavaScript + HTML5 Canvas. No package manager, bundler, transpiler, linter, or test framework — and none should be added without a clear need. The game must stay runnable as plain static files. UI text and the README are in Spanish.

## Running

No build or install step. Open `index.html` directly, or serve the repo root:

```bash
python -m http.server 8000   # then visit http://localhost:8000
```

There are no automated tests. Verify gameplay changes manually in a browser: movement, rotation near walls, soft/hard drop, line clears, scoring and level speed-up, pause/resume, restart, game over. Check the browser console for errors, and test a narrow viewport when touching UI.

## Architecture

Three files, each with one responsibility: `index.html` (DOM: board canvas, side panel, overlay), `style.css` (presentation), `game.js` (all logic). `game.js` is a single classic script (not a module) with top-level mutable globals (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, `animId`, …) shared by all functions.

- **Board**: `ROWS × COLS` matrix; `0` = empty, `1–7` = piece type, which doubles as the index into `COLORS`. `PIECES[type]` matrices store that same type number in their filled cells, so merging a piece writes its color index straight into the board.
- **Piece**: `{ type, shape, x, y }`. `shape` is a copied square matrix; rotation (`rotateCW`) replaces `shape` with a new matrix. `collide(shape, ox, oy)` is the single legality check used by movement, rotation, drops, ghost projection and spawn (cells with `y < 0` are allowed).
- **Rotation**: `tryRotate` tries horizontal kicks `[0, -1, 1, -2, 2]` — simple offsets, not SRS.
- **Piece lifecycle**: `lockPiece()` → `merge()` → `clearLines()` (updates lines/score/level/`dropInterval`) → `spawn()` (promotes `next`, rolls a new `next` with plain `Math.random`, triggers `endGame()` if the new piece already collides).
- **Loop**: `loop(ts)` via `requestAnimationFrame` accumulates `dt` into `dropAccum` and applies gravity when it exceeds `dropInterval`, then redraws the whole board every frame. Pause/game over work by cancelling `animId`.
- **Rendering**: `draw()` = grid → locked cells → ghost (`ghostY()`, alpha 0.2) → current piece. `drawNext()` renders into the separate `#next-canvas` on a 4×4 grid and only runs on spawn. The HUD is DOM text updated via `updateHUD()`.
- **Scoring**: `LINE_SCORES[cleared] * level`; soft drop +1/row, hard drop +2/row. Level = `floor(lines / 10) + 1`; `dropInterval = max(100, 1000 − (level − 1) × 90)`.

## Coupling to keep in sync

- `<canvas id="board">` width/height in `index.html` must equal `COLS × BLOCK` by `ROWS × BLOCK`.
- `#next-canvas` (120×120) assumes 4 cells × the hard-coded `NB = 30` in `drawNext()`.
- Keyboard controls are listed in three places: the `keydown` handler in `game.js`, the controls list in `index.html`, and the controls table in `README.md`.
- `game.js` looks up elements by id (`board`, `next-canvas`, `score`, `lines`, `level`, `overlay`, `overlay-title`, `overlay-score`, `restart-btn`), so renaming any of them breaks the script.

## Known quirks in the current code

- `togglePause()` never re-hides `#overlay` on resume, so the "PAUSA" overlay stays visible after unpausing.
- When game over is triggered from inside `loop()` (gravity lock), `endGame()` cancels the already-fired frame and `loop` then schedules the next one, so the loop keeps running after game over. Game over from a key press (hard/soft drop) does stop correctly.

## Conventions

These come from `AGENTS.md`: two-space indentation, semicolons, `camelCase` for functions and variables, `UPPER_SNAKE_CASE` for constants, kebab-case for any new asset files (put them in a named directory). Keep accessible labels and keyboard controls intact. Update `README.md` when controls or startup instructions change. Commits: short imperative messages, one change each.
