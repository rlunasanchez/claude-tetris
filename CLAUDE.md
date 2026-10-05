# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A classic Tetris implementation in vanilla JavaScript, HTML5 Canvas, and CSS. No dependencies, no build step, no package manager — just three files.

## Running the game

Open `index.html` directly in a browser, or serve it with any static server, e.g.:

```bash
python3 -m http.server 8000
npx serve .
php -S localhost:8000
```

There is no build, lint, or test tooling in this repo (no `package.json`).

## Architecture

Everything lives in `game.js` (~300 lines), driven by module-level mutable state (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, etc.) — there is no class structure or module system.

- **Board model**: `ROWS × COLS` matrix (20×10); each cell is `0` (empty) or a color index `1–7` identifying which piece locked there.
- **Pieces**: defined in `PIECES` as square matrices (I, O, T, S, Z, J, L). Rotation (`rotateCW`) is a transpose + row reverse, not a lookup table — there are no precomputed rotation states.
- **Collision** (`collide`): checks board bounds and overlap with locked cells for a shape at a given offset.
- **Wall kicks** (`tryRotate`): after rotating, tries offsets `[0, -1, 1, -2, 2]` until one doesn't collide, else the rotation is discarded.
- **Game loop** (`loop`): driven by `requestAnimationFrame`; accumulates elapsed time in `dropAccum` and advances the piece one row once `dropInterval` is exceeded, otherwise locks it (`lockPiece` → `merge` + `clearLines` + `spawn`).
- **Line clearing** (`clearLines`): scans bottom-up, splices out full rows and unshifts empty ones at the top; scoring uses `LINE_SCORES = [0,100,300,500,800]` multiplied by `level`. Level increases every 10 lines, which recalculates `dropInterval = max(100, 1000 - (level-1)*90)`.
- **Ghost piece** (`ghostY`): projects the current piece straight down until collision, drawn at `globalAlpha = 0.2`.
- **Rendering**: two canvases — `#board` (main play field) and `#next-canvas` (next-piece preview) — both drawn with the same `drawBlock` helper, just different block sizes.
- **Input**: a single `keydown` listener switches on `e.code` (arrows, `Space` for hard drop, `X` for alternate rotate, `P` for pause).

When tuning gameplay constants (`COLS`, `ROWS`, `BLOCK`, `LINE_SCORES`, initial `dropInterval`), they're all declared at the top of `game.js`. If `COLS`/`ROWS`/`BLOCK` change, the `#board` canvas `width`/`height` in `index.html` must be updated to match (`COLS × BLOCK`, `ROWS × BLOCK`).

Comments and identifiers in the codebase mix Spanish (README, some UI text) and English (code); keep that convention when editing.
