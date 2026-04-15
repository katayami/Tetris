# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Single-file browser Tetris game located at `Desktop/testfolder/index.html`. No build tools, dependencies, or package managers — open the file directly in a browser to run.

```
start "" "C:/Users/emil/Desktop/testfolder/index.html"
```

## Architecture

All game logic, styles, and markup live in one HTML file. Key sections in order:

- **CSS** — layout (flexbox: canvas + sidebar), palette-transition styles, message overlay
- **BLOCK size** — computed at startup from `window.innerHeight`/`innerWidth` so the game fills the viewport; canvas dimensions are set dynamically from this value
- **PALETTES array** — each entry holds bg, border, grid, ghost, label/value colors, and 7 block colors; `paletteIndex` selects the active one at draw time
- **Game state** — `grid` (2D array ROWS×COLS), `piece`/`nextPiece` (shape + position), `score`/`lines`/`level`, `dropInterval` (ms, decreases with level), `running`/`paused` flags
- **`drawBlock(ctx, x, y, colorId, size)`** — draws a single cell with a bevel effect (light top-left, dark bottom-right) for visible borders between adjacent same-colored blocks; uses a `GAP` constant for cell spacing
- **`loop(ts)`** — `requestAnimationFrame` loop; accumulates delta into `dropCounter`, locks piece and spawns next when counter exceeds `dropInterval`
- **Pause** — `Escape` calls `togglePause()`, which cancels/restarts the rAF loop and overlays "PAUSED" text on the canvas
