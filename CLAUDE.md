# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Tetris in vanilla JavaScript + HTML5 Canvas. No dependencies, no `package.json`, no build step, no linter, no tests. The README and UI text are in Spanish; keep user-facing strings in Spanish.

## Running

Open `index.html` directly, or serve statically (e.g. `python3 -m http.server 8000`) and visit `http://localhost:8000`.

## Architecture

Three files: `index.html` (DOM + two canvases), `style.css`, and `game.js` (all logic, a single script loaded as a classic `<script>` with `'use strict'`; no modules).

Things that span the code and are easy to miss:

- **Board/piece encoding**: the board is a `ROWS × COLS` matrix of `0` or a color index 1–8. The same index is the piece type, the value stored inside `PIECES[type]` shape matrices, and the index into `COLORS`. Piece 8 is the 3×3 "tuerca" (ring with an empty center). Adding a piece means updating `PIECES` and `COLORS`; `randomPiece` picks from `PIECES.length - 1` automatically.
- **Canvas size is duplicated**: `COLS`/`ROWS`/`BLOCK` in `game.js` must match the `width`/`height` attributes of `<canvas id="board">` (300×600) in `index.html`. The next-piece canvas is 120×120 and `drawNext` hardcodes a 4×4 cell area with a 30px block.
- **Game state lives in module-level `let` variables** (declared on one line near the top) and is fully reset by `init()`, which the restart button also calls. DOM element refs are grabbed once at load by id.
- **Loop control**: `loop` is driven by `requestAnimationFrame` and accumulates `dropAccum` against `dropInterval`. Pause and game over stop it with `cancelAnimationFrame(animId)`; unpausing restarts it manually via `togglePause`. Any new path that stops/starts the loop must keep `animId` and `lastTime` consistent.
- **Piece lifecycle**: `lockPiece` → `merge` → `clearLines` (updates lines/level/`dropInterval`/score) → `spawn`. `spawn` calls `endGame` if the new piece collides immediately. Both `hardDrop` and `softDrop` end in `lockPiece`, and the gravity step in `loop` duplicates the move-or-lock logic rather than calling `softDrop` (soft drop awards points; gravity does not).
- **Rotation** is clockwise only (`rotateCW`), with simple horizontal wall kicks `[0, -1, 1, -2, 2]` in `tryRotate` (not SRS).
- Input handling is a single `keydown` listener using `e.code`; it ends with `updateHUD()`.
