# Planning Benchmark

Benchmark how well coding agents plan against a real product spec. Feed it a PRD, get a plan, evaluate coverage, and generate a stakeholder-ready report.

## What This Repo Does

There is a product requirements document (PRD) in `docs/prd/`. You point a coding agent at it and ask it to produce an implementation plan. Then you evaluate how well that plan covers the PRD. Then you generate a visual report.

Each step is a self-contained prompt file. You run each step in a **fresh conversation** with the best model available.

## The Three Steps

### Step 1: Generate the Plan

Open a fresh conversation with your coding agent. Tell it:

> Read `1-START_HERE.md` and follow its instructions.

The agent will read all the PRD documents in `docs/prd/` and produce an implementation plan.

**Output:** `results/PLAN.md`

### Step 2: Evaluate the Plan

Open a **new conversation** (fresh context). Tell the agent:

> Read `2-EVALUATE_PLAN.md` and follow its instructions.

The agent will read both the PRD and the plan from Step 1, then audit the plan for coverage and alignment. It scores every requirement as full, partial, or missing.

**Requires:** `results/PLAN.md` from Step 1
**Output:** `results/PLAN_EVAL.md`

### Step 3: Generate the Report

Open a **new conversation** (fresh context). Tell the agent:

> Read `3-PLAN_EVAL_REPORT.md` and follow its instructions.

The agent will generate a stakeholder-ready HTML dashboard from the evaluation. This is a visual report designed to communicate coverage posture in under 60 seconds.

**Requires:** `results/PLAN_EVAL.md` from Step 2
**Output:** `results/PLAN_EVAL_REPORT.html`

## Why Fresh Conversations?

Each step consumes significant context. Starting fresh ensures the agent has maximum context window available for the task at hand. It also isolates each step so you can re-run one without re-running the others.

## Repo Structure

```
1-START_HERE.md              # Step 1 prompt — planning
2-EVALUATE_PLAN.md           # Step 2 prompt — evaluation
3-PLAN_EVAL_REPORT.md        # Step 3 prompt — report generation
docs/prd/                    # The product spec (PRD + supporting docs)
results/                     # All outputs land here
CLAUDE.md                    # Auto-loaded instructions for Claude Code
GEMINI.md                    # Auto-loaded instructions for Gemini
AGENTS.md                    # Auto-loaded instructions for other agents
INSTRUCTIONS.md              # Development guidelines (referenced by agent configs)
```
