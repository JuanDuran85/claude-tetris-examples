# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla-JS Tetris on HTML5 Canvas. No package.json, no build, no tests, no dependencies — three files: `index.html`, `style.css`, `game.js`. README is in Spanish; UI strings are Spanish.

## Running

Open `index.html` directly, or serve statically (`python3 -m http.server 8000`). There is nothing to install, lint or test — verification is running the page and playing it.

## Architecture notes

`game.js` is a single script (no modules, `'use strict'`) with all state in file-level `let` globals (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropAccum`, `dropInterval`, `animId`). `init()` is both first-run and restart: it resets every global, so any new piece of state must be reset there too or it survives across games.

Key invariants worth knowing before editing:

- **Piece type == color index.** `PIECES[n]` is filled with the literal `n`, so board cells store 1–7 and index straight into `COLORS`. Both arrays are 1-based with `null` at index 0; `drawBlock` treats 0 as empty and returns early. Adding a piece means appending to both arrays in the same order.
- **Rotation is stateless.** `rotateCW` returns a new matrix; `tryRotate` tests wall-kick offsets `[0,-1,1,-2,2]` and only commits on the first that doesn't `collide`. There is no SRS kick table or rotation-state tracking.
- **`collide(shape, ox, oy)` is the single geometry gate** — movement, rotation, ghost projection, soft drop and spawn-death all route through it. Board-shape or bounds changes belong here, not in callers.
- **Canvas sizes are duplicated in HTML.** `#board` is hardcoded `300x600` and must equal `COLS*BLOCK` x `ROWS*BLOCK`; `#next-canvas` is `120x120` because `drawNext` centers the piece in a fixed 4x4 grid of 30px blocks.
- **Loop**: `requestAnimationFrame` accumulates `dt` into `dropAccum` and drops one row per `dropInterval` ms. Pause/game-over work by `cancelAnimationFrame(animId)`; `togglePause` restarts the loop and must reset `lastTime` first or the accumulated pause time drops the piece instantly.
- Speed/level curve lives in `clearLines`: level = `lines/10 + 1`, `dropInterval = max(100, 1000 - (level-1)*90)`.
