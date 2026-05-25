# AGENTS.md — finance-planner

## Architecture

**Single-file app.** The entire application is `index.html` (~3300 lines). No build step, no bundler, no package manager, no separate JS/CSS files.

- Inline `<style>` in `<head>`
- Inline `<script>` at bottom of `<body>`
- Two external CDN deps: `chart.js` (unpinned) and `html2canvas@1.4.1` (pinned)

## Running the app

```bash
xdg-open index.html   # Linux
open index.html       # macOS
```

No server needed. Drag into any browser.

## No tooling

No `npm`, `node`, `pytest`, `make`, `lint`, `typecheck`, or CI. There is nothing to install and no commands to run besides opening the file in a browser.

## Key JS architecture

- `state = {}` — global object updated on every input change; holds `gross, pvdAmt, takeHome, weekly, commute, family, personal, dca, living, fixedTotal, buffer`
- `updatePlanner()` — main recalculation function, called on every slider/input event and on language switch
- `updateRecommendation()` — called from `updatePlanner()` only when recommendation tab is active
- `currentLang` — global `"en"` or `"th"`, switched by `setLang()`
- `fmt(n)` — formats numbers via `toLocaleString('th-TH')`
- `thb(n)` — appends `" THB"` or `" บาท"` based on `currentLang`
- `pctOf(val, total)` — returns rounded integer percentage

## Critical calculation facts

- `takeHome = gross - pvdAmt`
- `living = (weekly * 52) / 12 + commute` — weekly uses 52/12 weeks, NOT ×4
- `buffer = takeHome - living - family - fixedTotal - personal - dca` — auto-calculated, slider is disabled
- **All `%` pills (sliders, fixed costs, recommendation tab breakdown) are relative to `takeHome`, not `gross`**

## Bilingual pattern

Every user-visible string uses `data-en="…" data-th="…"` attributes on the element. `setLang()` swaps them and then calls `updatePlanner()` + `renderCostList()`. New UI text must follow this pattern — never hardcode a single language in HTML.

## Fixed costs

`fixedCosts` array holds `{ name, name_th, amount }`. Items store both language names. `renderCostList()` rebuilds the DOM. `getTotalFixed()` sums amounts.

## Export / import

- `exportJSON()` / `importJSON()` — serialises all slider values + `fixedCosts` + `lang`; import requires `version: 1` field
- `exportPNG()` — captures active `.tab-panel` via `html2canvas` at scale 2
- `exportPDF()` — makes all tab panels visible, calls `window.print()`, restores state in `onafterprint`

## Gross salary floor

`updatePlanner()` uses `Math.max(10000, +grossInput.value || 0)` — effective minimum is 10,000 regardless of input.

## Chart instances

- `allocChart` — doughnut, initialised on page load
- `projChartInstance` — line chart, lazily built on first switch to recommendation tab; destroyed/rebuilt by `refreshProjChart()`; any DCA change must call `refreshProjChart()` if recommendation tab is active

## Commit style

Conventional commits: `feat:`, `fix:`, `docs:` — lowercase, imperative.

## Plans

Implementation plans are saved to `.opencode/plans/`.
