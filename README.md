# Planning Benchmark

Benchmark how well coding agents plan against a real product spec. Feed it a PRD, get a plan, evaluate coverage, and generate a stakeholder-ready report.

## How It Works

There are three steps. Each step should be run in a **fresh context** with the best model available.

### Step 1: Plan

Open your coding agent and tell it to read `1-START_HERE.md` and follow its instructions. The agent will read the PRD in `docs/prd/` and produce an implementation plan.

**Output:** `results/PLAN.md`

### Step 2: Evaluate

Open a **new context** (fresh conversation) and tell the agent to read `2-EVALUATE_PLAN.md` and follow its instructions. It will read the PRD and audit the plan for coverage and alignment.

**Output:** `results/PLAN_EVAL.md`

### Step 3: Report

Open a **new context** and tell the agent to read `3-PLAN_EVAL_REPORT.md` and follow its instructions. It will generate a visual HTML report from the evaluation.

**Output:** `results/PLAN_EVAL_REPORT.html`

## What's In Here

```
1-START_HERE.md          # Step 1: Planning prompt
2-EVALUATE_PLAN.md       # Step 2: Evaluation prompt
3-PLAN_EVAL_REPORT.md    # Step 3: Report generation prompt
docs/prd/                # The product spec (PRD + supporting docs)
results/                 # All outputs land here (PLAN.md, PLAN_EVAL.md, PLAN_EVAL_REPORT.html)
```
