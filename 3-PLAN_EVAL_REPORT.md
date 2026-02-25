# VISUALIZE GAPS

## Overview
This file is used to visualize the gaps in the plan. It is used to tell the coverage story to stakeholders who will never read the raw tables. This step is not used until after an entire plan has been written and the coverage has been evaluated.

## Gaps / Coverage File Location

The file is called `PLAN_EVAL.md`. It is in the `results/` folder.

## Instructions
Build a single-file HTML page that tells the coverage story to stakeholders who will never read the raw tables.
Audience: A product lead or VP who needs to understand in under 60 seconds — what does this plan cover well, where are the risks, and how confident should they be in this plan's readiness?
The page must communicate ALL of the following (how you present them is up to you):
1) The overall coverage score — make it prominent and immediately legible.
2) The before/after arc — show how coverage changed from the initial plan to the revised plan. This is the narrative spine: "we started here, we identified gaps, we improved to here."
3) What's strong — which areas of the PRD are well-covered? Give stakeholders confidence about what IS handled.
4) What's at risk — which requirements are still partial or missing after revision? Don't bury this. Stakeholders need to see the risks, not hunt for them.
5) The top gaps and why they matter — not just that something is missing, but what breaks or degrades without it.

Design guidance:
- This should feel like a stakeholder-ready dashboard or briefing page, not a raw data dump.
- You have full creative freedom on layout, visual style, chart types, color palette, typography, and how you organize the narrative. Make it yours.
- Prioritize visual hierarchy — the most important information should hit first.
- Think about how you'd want to present this in a meeting. What do you lead with? What's the flow?
- No external dependencies (no CDN links). Everything self-contained.
- Should look polished at 1200px wide (screenshot-ready for video).

Do not just render the coverage table with colored rows and call it done. Tell the story.

## Output Location

Write the HTML report to: `results/PLAN_EVAL_REPORT.html`