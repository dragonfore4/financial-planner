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

- `state = {}` — global object updated on every input change; holds `gross, pvdPct, pvdAmt, ssoAmt, deductTotal, takeHome, weekly, commute, family, personal, dca, living, fixedTotal, buffer`
- `updatePlanner()` — main recalculation function, called on every slider/input event and on language switch
- `updateRecommendation()` — called from `updatePlanner()` only when recommendation tab is active
- `currentLang` — global `"en"` or `"th"`, switched by `setLang()`
- `fmt(n)` — formats numbers via `toLocaleString('th-TH')`
- `thb(n)` — appends `" THB"` or `" บาท"` based on `currentLang`
- `pctOf(val, total)` — returns rounded integer percentage

## Critical calculation facts

- `takeHome = gross - pvdAmt - ssoAmt - deductTotal`
- `ssoAmt = Math.min(Math.round(gross * 0.05), 750)` — SSO is 5% of gross, capped at 750
- `deductTotal = getTotalDeductions()` — sum of custom pre-take-home deductions (e.g. ESPP, tax, loan)
- `living = (weekly * 52) / 12 + commute` — weekly uses 52/12 weeks, NOT ×4
- `buffer = takeHome - living - family - fixedTotal - personal - dca` — auto-calculated, slider is disabled
- `savingsRate = (pvdAmt + dca + Math.max(0, buffer)) / gross` — relative to **gross**, and PVD counts as savings (it's retirement saving)
- **Most `%` pills (expense sliders, fixed costs, recommendation tab spending breakdown) are relative to `takeHome`.** Exceptions: **PVD, SSO, and custom deductions** are relative to `gross` (they are salary deductions taken before take-home).

## Bilingual pattern

Every user-visible string uses `data-en="…" data-th="…"` attributes on the element. `setLang()` swaps them and then calls `updatePlanner()` + `renderCostList()`. New UI text must follow this pattern — never hardcode a single language in HTML.

## Fixed costs

`fixedCosts` array holds `{ name, name_th, amount }`. Items store both language names. `renderCostList()` rebuilds the DOM. `getTotalFixed()` sums amounts.

## Custom deductions

Pre-take-home salary deductions (e.g. ESPP, tax, loan), added in the Salary Setup card. Mirrors the fixed-costs pattern.

- `customDeductions` array holds `{ name, name_th, amount }` (empty by default, no seed row)
- `renderDeductList()` rebuilds the `#deductList` DOM; `getTotalDeductions()` sums amounts
- `addDeductRow()` / `removeDeduct(i)` / `updateDeductName(i, value)` mirror the cost-row functions
- Subtracted from gross **before** take-home (`takeHome = gross - pvdAmt - ssoAmt - deductTotal`), so they cascade to every take-home-relative pill, the doughnut chart, and the recommendation tab automatically — they are NOT a separate doughnut segment
- Their own `%` pills are relative to **gross** (like PVD/SSO), not take-home
- Recommendation tab renders deduction rows into `#rb-deduct-rows`

## Export / import

- `exportJSON()` / `importJSON()` — serialises all slider values + `fixedCosts` + `customDeductions` + `lang`; import requires `version: 1` field (older files without `customDeductions` load as empty)
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
