# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Single-file browser Tetris game. Hosted on GitHub Pages: https://katayami.github.io/Tetris/
Local file: `C:/Users/emil/Desktop/testfolder/index.html`

```
start "" "C:/Users/emil/Desktop/testfolder/index.html"
```

No build tools, dependencies, or package managers — open the file directly in a browser to run.

## Architecture

All game logic, styles, and markup live in one HTML file (`index.html`). Key sections in order:

- **CSS** — flexbox layout (board + sidebar), mobile media query, message/leaderboard overlays, mobile controls, `#board-wrap` + `#next-overlay` for mobile Next piece
- **THEME object** — single warm color palette (`bg`, `border`, `grid`, `ghost`, `label`, `value`, `body`, `blocks[1..7]`); no palette switching
- **BLOCK size** — computed at startup from `window.innerHeight`/`innerWidth`; different formula for mobile vs desktop (`isMobile` flag)
- **DPR scaling** — both `board` and `next-canvas` are scaled by `devicePixelRatio` for Retina sharpness
- **`NEXT_SIZE = BLOCK * 3`**, **`PREVIEW_BLOCK = BLOCK * 0.75`** — next piece canvas size and block size for preview
- **Game state** — `grid` (2D array ROWS×COLS), `piece`/`nextPiece`, `score`/`lines`/`level`, `dropInterval`, `running`/`paused`, `playerName`
- **`drawBlock(context, x, y, colorId, size)`** — rounded block with `roundRect`, top highlight + bottom shadow bevel
- **`drawNextPiece(ctx2d)`** — renders next piece centered by bounding box into any context; called for both sidebar canvas and mobile overlay canvas
- **`loop(ts)`** — `requestAnimationFrame` loop; accumulates delta into `dropCounter`
- **Pause** — `Escape` / pause button calls `togglePause()`; on pause, board is fully hidden (filled with `THEME.bg` + "PAUSED" text)
- **Firebase Firestore** — leaderboard via `firebase-app-compat` + `firebase-firestore-compat` (CDN v10.12.0); player name stored in `localStorage`; score only updates if new score > existing; `submitScore()` / `loadLeaderboard()`
- **Sound** — Web Audio API, procedural sounds in `SFX` object (`move`, `rotate`, `lock`, `clear`, `over`)
- **Mobile controls** — `#mobile-controls` div with touch buttons (←, ↻, ↓, →); shown only on mobile via CSS
- **Mobile Next overlay** — `#next-overlay` inside `#board-wrap` (position: absolute, top-right corner); hidden on desktop, shown on mobile; uses separate `next-canvas-overlay` canvas rendered by `drawNextPiece()`

## Firebase config

```js
firebase.initializeApp({
  apiKey: "AIzaSyBddViRdJjDOWzSVFjvBPDCg58geTUVevU",
  projectId: "tetris-leaderboard-540c8",
  ...
});
```

Firestore rule: score can only increase (`allow update if newScore > existingScore`).

## Controls

| Action | Key |
|--------|-----|
| Move left/right | Arrow keys or A/D (layout-independent via `e.code`) |
| Rotate | Arrow Up or W |
| Soft drop | Arrow Down or S |
| Hard drop | Space |
| Pause | Escape |
| Restart | R |
