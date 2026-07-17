# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Classic Tetris implemented in vanilla JavaScript with HTML5 Canvas. No dependencies, no build step, no package.json — just static files served or opened directly.

## Running the game

```bash
open index.html                # macOS, opens directly in browser
python3 -m http.server 8000    # or serve locally, then visit localhost:8000
```

There are no tests, linters, or build/bundle commands in this repo.

## Architecture

Three files, all logic lives in `game.js` (~300 lines):

- **`index.html`** — DOM structure: main `#board` canvas (300×600, 10×20 cells at `BLOCK`=30px), a `#next-canvas` preview, HUD spans (`#score`, `#lines`, `#level`), and a hidden `#overlay` for pause/game-over.
- **`style.css`** — dark/retro arcade visual theme only; no logic.
- **`game.js`** — entire game: board model, piece definitions, input handling, game loop, rendering. All state lives in module-level `let` variables (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, etc.) — there is no class/state container, so functions read/mutate these globals directly.

### Key mechanics (game.js)

- **Board model**: `ROWS × COLS` matrix; each cell is `0` (empty) or a piece color index (1–7).
- **Pieces**: defined as square matrices in `PIECES`; `rotateCW` rotates via transpose + row reverse.
- **Collision** (`collide`): checks board bounds and overlap with locked cells.
- **Wall kicks** (`tryRotate`): on rotation collision, tries offsets `[0, -1, 1, -2, 2]` before giving up.
- **Game loop** (`loop`): driven by `requestAnimationFrame`, accumulates elapsed time and drops the piece one row when `dropAccum >= dropInterval`.
- **Line clearing** (`clearLines`): scans bottom-to-top, splices full rows, unshifts empty rows at top.
- **Scoring**: `LINE_SCORES = [0, 100, 300, 500, 800]` × current level; hard drop = 2 pts/cell, soft drop = 1 pt/row.
- **Leveling**: level = `floor(lines / 10) + 1`; `dropInterval = max(100, 1000 - (level-1)*90)` ms.
- **Ghost piece** (`ghostY`): projects current piece straight down, drawn at `globalAlpha = 0.2`.

Flow: `init()` builds the board, seeds `next`, calls `spawn()`, and starts the `loop`. `spawn()` promotes `next` to `current` and generates a new `next`; if the new `current` immediately collides, `endGame()` fires and the overlay shows GAME OVER. `keydown` handles movement/rotation/soft-drop/hard-drop; `KeyP` toggles pause independent of `gameOver`/`paused` guards.

## Tunable constants (game.js top)

`COLS`, `ROWS`, `BLOCK`, `COLORS`, `LINE_SCORES`, `dropInterval` (initial). If `COLS`/`ROWS`/`BLOCK` change, update the `#board` canvas `width`/`height` in `index.html` to match (`COLS × BLOCK`, `ROWS × BLOCK`).
