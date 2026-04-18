# Planning Benchmark Dashboard — Purpose & Design Charter

This document defines what the dashboard at `index.html` is for, who it serves,
and the questions it must answer. It is the spec the dashboard is built
against. Edits to the dashboard should reconcile with this charter; if the
charter needs to change, change it here first.

## Audience

1. **The author**, during and after a YouTube recording session. Needs to
   answer "who wins" in one glance and drive a controlled comparison between
   2–6 models live on camera.
2. **Research-minded viewers** reaching the page via a video link. Want to
   poke at the data themselves, see the same rankings, and confirm the story
   the video told.
3. **Benchmark contributors** sanity-checking that newly published runs
   landed correctly and grouped into the right series/case.

The first audience dominates design decisions. If a choice serves the video
use case, it ships; if it serves only a hypothetical researcher, it's
optional.

## The six questions the dashboard must answer

A viewer landing cold must be able to answer these without hunting:

1. **Who is winning today?** — single headline leader, with silver and
   bronze visible without scrolling.
2. **How do the top models compare?** — pairwise or 3-way score delta,
   including per-area strengths and weaknesses.
3. **Does planning mode / effort / tool matter?** — controlled comparisons
   where only one variable changes.
4. **What is the cost–quality trade-off?** — score vs. runtime (and cost
   when available), with the Pareto frontier visible.
5. **Where does each model break down?** — top gaps and weakest areas for
   a single model, linkable to the full evaluation report.
6. **What is in the benchmark and how stable are the results?** — run count,
   taxonomy version, replicate coverage, and honesty about small-N.

Any section that doesn't serve one of these six questions is decorative.

## Design principles

- **Readable on a 1080p video frame.** Body text ≥ 14px, headline metrics
  ≥ 32px. No metadata chip shrinks below 12px. High-contrast type, no thin
  weights at small sizes.
- **One click to compare.** Any model visible anywhere in the dashboard
  should be addable to the Compare Tray with a single click. No tab walk.
- **Tray is the comparison model.** Up to 6 slots. All comparison surfaces
  (radar, delta, heatmap highlights) read from the tray. Emptying is one
  click.
- **Shareable URLs.** The current view, filters, and tray contents must
  serialize into the query string so `?view=frontier&tray=run_a,run_b` loads
  the same state.
- **Record Mode.** A toggle that enlarges type, drops chrome, and isolates
  the currently active view for clean screen capture. Record Mode is a
  first-class feature, not a bolt-on.
- **Honest about N.** When comparisons rest on a single replicate, say so.
  Don't imply precision the data doesn't have.
- **No build step.** Single `index.html`, ECharts via CDN, data via
  `data.js` window global. GitHub Pages must serve it without tooling.

## Primary views

1. **Hero Leaderboard** — persistent across views. Top 3 models by the
   currently selected metric, large cards, click-to-add to tray.
2. **Leaderboard detail** — full ranked list with mini sparkline of per-area
   scores and a controlled/uncontrolled marker for each row.
3. **Efficiency Frontier** — score vs. planning runtime, bubble size
   encodes tool-call count (cost when we have it). Pareto front drawn.
   Zoomable, pannable, click a point to add to tray.
4. **Area Heatmap** — models × functional areas matrix, cell color =
   score in that area. Click a row to add to tray; click a column to
   deep-dive into that area.
5. **Deep Dive** — single-model view. Scores by severity, per-area
   breakdown, top gaps, narrative summary fields, links out to the full
   PLAN and PLAN_EVAL artifacts. Full PLAN_EVAL_REPORT.html lives at its
   own URL; the dashboard links out rather than embedding.

## Compare Tray behaviour

- Sticks to the top of the scrollable content area. Always visible.
- Max 6 slots. Attempting to add a 7th gives feedback but does not silently
  drop.
- When tray has 2+ entries, a comparison panel unfolds beneath the tray
  showing (a) overall score bars, (b) per-severity score deltas, and
  (c) a radar of per-area scores when taxonomy versions match.
- "Controlled?" badge on the comparison panel is green when all tray items
  share the same taxonomy version and differ in exactly one of
  {model, tool, effort, planning mode}. Amber otherwise, with a tooltip
  naming the varying dimensions.

## Explicit non-goals

- Historical trends over release dates. The benchmark is too new, the data
  too sparse, and Trends was doing more harm than good in v2. If we add a
  time axis back later, it must answer a specific question on this list.
- Per-requirement navigation inside the dashboard. The full report
  (`PLAN_EVAL_REPORT.html`) handles that. The dashboard links out.
- User accounts, saved views, or annotation. URL params carry the state we
  need; anything beyond that is scope creep.
- Mobile-first layout. Target is desktop recording (1920×1080 minimum).
  Mobile degrades gracefully but is not optimised.

## Versions and file layout

The results repo preserves prior dashboard versions so we can cite them
from videos and compare design evolution.

| File | What it is |
|------|------------|
| `index.html` | Current dashboard (v3). Built against this charter. |
| `v2.html` | Prior dashboard (dark gold/sage, 5-tab). Preserved for reference. |
| `index_orig.html` | Earliest dashboard (light beige/teal, pre-dark-theme). |
| `data.js` / `dashboard-data.json` | Same dataset in JS-global and JSON forms, produced by `tools/build_dashboard_data.py` in the control repo. |
| `DASHBOARD_PURPOSE.md` | This file. |

Each published dashboard links in its footer to the prior versions.

## Data source contract

The dashboard reads `window.__DASHBOARD_DATA__` (loaded via `data.js`).
The shape it relies on:

- `generated_at` — timestamp for the "data as of" footer.
- `summary` — aggregate counts for the honesty-about-N strip.
- `runs[]` — one entry per executed benchmark run, with
  `scores{overall,critical,important,detail}` (0–100),
  `area_scores[]`, `top_gaps[]`, `narrative{}`, and `artifacts{}` (links
  out to PLAN/PLAN_EVAL/PLAN_EVAL_REPORT).
- `series[]` and `cases[]` — grouping metadata. The dashboard displays
  these but does not re-aggregate; the pipeline is the source of truth
  for case and series rollups.

If the pipeline shape changes, update this section before touching the
dashboard.
