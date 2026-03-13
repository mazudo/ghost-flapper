# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A collection of single-file browser games — no build system, no dependencies, no server required. Each game is a self-contained `.html` file that runs by opening it directly in a browser.

## Running the Games

Open any `.html` file directly in a browser:
- `ghost-flapper.html` — spooky Flappy Bird clone
- `tictactoe.html` — two-player Tic Tac Toe

There is no build step, no `npm install`, and no dev server.

## Git & GitHub Workflow

- Remote: `git@github.com:mazudo/ghost-flapper.git` (SSH)
- **Always ask the user for confirmation before pushing to GitHub.**
- Commit locally freely; push only with explicit approval.
- Use clean, descriptive commit messages that explain *why*, not just *what*.

## Architecture: ghost-flapper.html

Everything lives in one `<script>` block. The code is organized into these sections (in order):

1. **Canvas setup** — virtual resolution `480×640`, scaled to fit the window via `ctx.scale()` every frame. All draw coordinates use the virtual resolution regardless of actual window size.
2. **DIFFICULTY config** — single source of truth for all tunable values (speed, gap, spawn interval). `rampDifficulty()` reads from here and is called every `increaseEveryNPipes` pipes cleared.
3. **State machine** — `gameState` cycles through `START → PLAYING → DEAD → NAME_ENTRY → GAME_OVER`. Each state has its own update/render branch in `gameLoop()`.
4. **Ghost class** — physics (gravity + flap impulse), squish spring animation, wavy-tail wobble via `wobbleT`, death tumble. `getHitbox()` returns a margin-shrunk AABB for forgiving collision.
5. **Pipe class** — scrolls left, holds the gap geometry, draws stone castle pillars with battlements and an animated bat. `passed` flag is set once for scoring.
6. **Background** — drawn every frame: sky gradient, moon, twinkling stars (each with an independent phase), ground strip, and parallax gravestones scrolling at 30% of `bgOffset`.
7. **Collision** — AABB `rectsOverlap()` against pipe top/bottom rects and screen bounds.
8. **High scores** — `localStorage` key `ghostFlapper_scores`, JSON array of `{ name, score }`, max 5 entries sorted descending. Read/written by `loadScores` / `saveScores` / `insertScore` / `qualifiesForHighScore`.
9. **Name entry** — 3-char slots, A–Z cycling. `nameChars[]` + `nameCursor` drive the UI; confirmed with Enter.
10. **Game loop** — `requestAnimationFrame` loop; applies `ctx.scale(getScale(), getScale())` each frame so all drawing is in virtual coordinates.

## Architecture: tictactoe.html

Pure DOM game (no canvas). State is three variables: `board` (9-element array), `current` (`'X'`/`'O'`), `over` (boolean). `WINS` is a hardcoded array of the 8 winning index triples. Score persists across rounds in a `score` object but is not saved to `localStorage`.
