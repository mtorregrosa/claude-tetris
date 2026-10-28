# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas). No build, no deps, no package.json, no tests, no linter. README.md is in Spanish; UI strings are Spanish too.

## Run

Open `index.html` directly, or serve statically: `python -m http.server 8000` → http://localhost:8000.

## Architecture

Three files: `index.html` (DOM + two canvases), `style.css`, `game.js` (all logic, single script, `'use strict'`, global state).

- `game.js` looks up DOM elements by id at load (`board`, `next-canvas`, `score`, `lines`, `level`, `overlay`, `overlay-title`, `overlay-score`, `restart-btn`). Renaming ids in `index.html` breaks it.
- Board canvas is `width=300 height=600` in HTML; must equal `COLS*BLOCK` × `ROWS*BLOCK` if those constants change. Next-piece canvas is 120×120 (4×4 cells of 30px).
- Board = `ROWS×COLS` matrix; cell is `0` or piece type 1–7, which indexes both `COLORS` and `PIECES` (index 0 is `null` in both).
- Game state is module-level `let` vars, reset in `init()` (also the restart handler).
- Loop: `loop(ts)` via `requestAnimationFrame`, `dropAccum` vs `dropInterval`. `animId` is cancelled on pause/game over; unpausing manually re-calls `loop`.
- Flow: `lockPiece()` = `merge()` → `clearLines()` → `spawn()`; `spawn()` calls `endGame()` if the new piece collides. `clearLines` also handles level/speed (`max(100, 1000-(level-1)*90)`).
- Rotation: `rotateCW` + `tryRotate` with horizontal kicks `[0,-1,1,-2,2]` (no SRS).
- Scoring: `LINE_SCORES[cleared]*level`, soft drop +1/row, hard drop +2/row.
- Keyboard handling is in one `keydown` listener using `e.code`; no key-repeat/DAS logic beyond browser defaults.
