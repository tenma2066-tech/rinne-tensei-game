# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

This is the public hosting folder for 輪廻転生ランダムリセマラゲーム (a Japanese-language browser gacha game where the player spends "lifespan" to reroll their reincarnated country, gender, face, family, and job, then receives a final life "rank"). It is deployed as-is via GitHub Pages at https://tenma2066-tech.github.io/rinne-tensei-game/.

There is no build step, package manager, or test suite — the entire game is a single static HTML file.

## Repository structure

- `index.html` — the entire game: HTML shell, all CSS (in a `<style>` block), and all JS (in a single `<script>` block). This is the only file to edit for gameplay, UI, or content changes.
- `robots.txt` — allows all crawlers, points to `sitemap.xml`.
- `sitemap.xml` — single-URL sitemap for the published site; update `<lastmod>` when publishing meaningful content changes.
- `.nojekyll` — disables GitHub Pages' Jekyll processing so files are served as-is.
- `README.md` (Japanese) — explains how to deploy this folder to GitHub Pages or Netlify Drop; there is no separate build/publish pipeline beyond uploading these files.

## Development workflow

There is no build/compile/lint/test tooling in this repo. To develop:
- Edit `index.html` directly.
- Preview by opening `index.html` in a browser directly, or serving the folder locally (e.g. `python3 -m http.server`) and visiting it — no dependencies to install.
- Deployment is simply publishing these static files (GitHub Pages serves directly from the `main` branch root, per the README).

When changing SEO/meta content (title, description, OGP tags, JSON-LD in `<head>`), keep `index.html`'s meta tags, `robots.txt`, and `sitemap.xml` consistent with the canonical URL.

## Code architecture

`index.html`'s script implements a small hand-rolled state machine + re-render-from-scratch UI (no framework):

- **Global mutable `state` object** (`getInitialState()`) holds `screen` (`'title' | 'gacha' | 'gameover' | 'result'`), `lifespan` (starts at 100), `stepIndex` (position within `stepsInfo`), per-category `results`, remaining `freeRolls` per category, `currentRollResult`, and `isRolling`.
- **`gachaData`** defines the five gacha categories (`country`, `gender`, `face_male`, `face_female`, `family`, `job`), each an array of rank tiers (`PLR` > `SSR` > `SR` > `R` > `N`) with a probability weight (`prob`, sums to 100 within a category), a `point` value used for scoring, an `items` pool of possible result names, and flavor `text`. Country/job name pools are defined separately (`countries`, `jobs`) and referenced from `gachaData`.
- **`stepsInfo`** defines the fixed order of gacha steps the player progresses through: country → gender → face → family → job. `face` dynamically switches between the `face_male`/`face_female` data based on the already-rolled gender.
- **`roll(typeKey)`** does weighted-random selection over a category's rank tiers, then a random pick within that tier's `items`.
- **`handleDraw()`** consumes a free roll or 20 years of lifespan, ends the game (`screen = 'gameover'`) if lifespan hits 0, then runs a `setInterval`-based slot-machine animation (cycling random names for ~1s) before settling on the real `roll()` result.
- **`handleNext()`** commits `currentRollResult` into `state.results[step]` and advances `stepIndex`, or moves to the `result` screen after the last step.
- **`calculateFinalScore()`** sums `lifespan` plus each result's `point`; **`getRank()`** maps score/lifespan thresholds to a final letter rank (`E` through `SS`); **`getSpecialTitle()`** grants a flavor title based on combinations of rolled ranks/names (e.g. all PLR/SSR, all N, specific name+rank combos).
- **`render()`** is called after every state mutation and does a full `appElement.innerHTML` rewrite based on `state.screen` — there is no diffing or component structure. Button `onclick` handlers are inlined as strings (e.g. `onclick="handleDraw()"`) calling the global functions directly, so those functions must stay in global scope.

When adding a new gacha category or step, update `gachaData`, `stepsInfo`, the `results`/`freeRolls` shape in `getInitialState()`, the `current-status` display in `render()`'s `gacha` screen, and the final `story` template in the `result` screen together — they all assume the same fixed set of category keys.

Rank tiers have corresponding CSS classes (`.rank-PLR`, `.rank-SSR`, `.rank-SR`, `.rank-R`, `.rank-N`, `.rank-none`) and flash-effect classes (`.flash-PLR`, `.flash-SSR`, `.flash-normal`) driving the visual rarity presentation — keep new/changed ranks in sync with these styles.

## Language and content conventions

All player-facing text (UI labels, gacha flavor text, story text, guide character dialogue) is in Japanese and should stay in Japanese for consistency. The game's tone is comedic/satirical (e.g. job flavor text ranges from "ノーベル賞受賞学者" to "ニート", "パチプロ").
