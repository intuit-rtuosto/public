# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A public-facing NB MRR (New Bookings Monthly Recurring Revenue) Pacing Calculator. It's a self-contained, static web application for revenue forecasting and scenario planning — no build step, no backend, no package manager.

## Repository layout

```
tools/nb-mrr-calculator.html   # Main calculator (all HTML/CSS/JS inline, ~1180 lines)
tutorials/nb-mrr-tutorial.html # Educational guide explaining NB MRR pacing concepts
```

## How to run

```bash
cd tools && python3 -m http.server
```

An HTTP server is required for Chart.js (loaded from CDN). Opening `nb-mrr-calculator.html` via `file://` will show fallback text instead of charts.

## Architecture

Both files are fully self-contained single-page HTML documents with inline CSS and JS. There is no build process, bundler, or framework.

**nb-mrr-calculator.html** has three logical sections:
1. **Styles** (lines 1–200) — CSS custom properties define the color system (`--low`, `--high`, `--baseline`, `--traffic`, `--growth`, `--web`)
2. **HTML** (lines 200–370) — Dashboard layout: sidebar inputs + main content area with chart, tables, comparison cards, and cohort table
3. **JavaScript** (lines 370–end) — Calculation engine, Chart.js rendering, UI state management

**Key JavaScript architecture:**
- `calculate(inp)` — Pure function: takes parsed inputs, returns full scenario model (baseline, low, high, monthly breakdowns, cumulative totals, exit rates)
- `recalc()` — Orchestrator: reads DOM inputs → `calculate()` → renders all outputs; debounced at 80ms
- `getViewData(d)` — Transforms calculation output for the active metric view (total vs. workstream)
- `WS_ALLOC` — Workstream allocation object (`{traffic, growth, web}`) updated by the 3-segment slider

**External dependency:** Chart.js 4.4.7 via `cdn.jsdelivr.net` (with graceful degradation if unavailable)

## Key calculation logic

The core funnel conversion and scenario ramp live in `calculate()` (~line 466):

- **Baseline NB MRR** = `weeklyTraffic × 52/12 × T2A × A2B × AOV` (each adjusted by confidence %)
- **Ramp factor** differs by scenario mode:
  - "In-Year Annual": `12/78` — distributes total lift across months 1→12 (sum 1+2+…+12 = 78)
  - "Exit Growth": `1/12` — uniform monthly increment
- **Exit gain %**: In-year mode uses `changePct × 144/78`; exit mode uses `changePct` directly
- **Recognized revenue**: Weekly cohort table normalizes each week's cohort contribution by actual weeks-per-month (precomputed `monthWeekCounts`), so the 52-week total equals the analytic formula: `annualNBMRR × 6.5`

**Fiscal year**: August–July (constant `FY_MONTHS`)

## Development notes

- All state lives in module-level variables (`activeMetric`, `scenarioMode`, `tableView`, `charts`, `WS_ALLOC`). There is no framework state management.
- The allocation slider is a custom 3-segment drag implementation — handle positions map to percentage splits that must sum to 1.0.
- When modifying calculation logic, update both the monthly ramp in `scenarioCalc()` and the cohort table in `renderRecognizedRevenueTable()` to stay consistent.
- `nb-mrr-tutorial.html` cross-links to `nb-mrr-calculator.html` and vice versa; keep filenames stable.
