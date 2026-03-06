# PLAN EVALUATION

This evaluation step is not used until after an entire plan has been written.

## PRD Info

The PRD is multiple files. All files are very important. You will find the PRD files in the `docs/prd/` folder. Start with `docs/prd/product_prd.md`, then read `docs/prd/infra_rider_prd.md`, then all files in `docs/prd/supporting_docs/`. For evaluation purposes, all PRD files must be ingested to fully understand the scope of the request itself.

## Instructions

Using the original PRD as the source of truth, audit the plan found in the `results/` folder for coverage and alignment. The plan is called `PLAN.md`.

Your output MUST follow this exact structure, in this order:

### 1. Requirements Extraction

Extract requirements from the PRD using a two-pass process. Both passes are mandatory.

#### Pass 1: Identify Functional Areas

Read through all PRD files and identify every distinct functional area. A functional area is a named grouping of related behavior — e.g., "Collection Management," "Search & Discovery," "AI Chat," "Data Persistence," "Settings & Configuration."

List each functional area with a short label. You should typically find 6–15 functional areas depending on PRD scope. These areas are organizational scaffolding — they structure your extraction but do not appear in the final scoring.

#### Pass 2: Extract Requirements Within Each Area

For each functional area, walk through the PRD files and extract every requirement. Requirements are extracted bottom-up from the document, not top-down from your interpretation.

**What counts as one requirement:**

Every requirement MUST be anchored to a specific, citable location in the PRD. For each requirement, you must be able to point to the file name, section heading, and the specific bullet point, sentence, or statement it came from.

- One bullet point or distinct statement in the PRD = one requirement, unless it contains multiple independently testable behaviors, in which case split it.
- If the same behavior is stated in multiple PRD locations, count it once and cite the primary location.
- Sub-bullets that add independent, testable behavior beyond their parent are separate requirements. Sub-bullets that merely elaborate or give examples are not.
- A requirement you cannot anchor to a specific PRD location does not exist. Do not extract it.

**Merge rule:** If two PRD statements describe the same testable outcome from different angles, they are one requirement. Cite both locations but assign one ID.

**Split rule:** If one PRD statement contains multiple behaviors that could independently pass or fail (e.g., "search supports shows and people with poster grid results"), split into separate requirements, each citing the same source statement.

For each requirement, provide:
- **ID**: Stable sequential ID (PRD-001, PRD-002, ...)
- **Area**: The functional area from Pass 1
- **Label**: Short description (8–12 words max)
- **Source**: PRD file name and section heading (e.g., `show_detail.md > My Data Toolbar`)
- **Severity**: Exactly one of:
  - `critical` — The product does not function or ship without this. Core user workflows break. A stakeholder would reject the deliverable.
  - `important` — The product works but is significantly degraded. Users notice and it materially hurts the experience or violates a stated constraint.
  - `detail` — Fit-and-finish, edge cases, or polish. The product is usable without it, but the spec asked for it.

**Severity calibration guidance:**
- If the PRD uses language like "must", "required", "non-negotiable", or "breaks without" — that is almost certainly `critical`.
- If the PRD describes a specific UX behavior, guardrail, or quality bar — that is typically `important`.
- If the PRD specifies formatting, defaults, labels, or fallback copy — that is typically `detail`.
- When in doubt between two tiers, choose the higher severity. It is better to over-weight a requirement than to under-weight it.

**Output format for this section:**

Group requirements by functional area. Under each area heading, list requirements as:

```
- PRD-001 | `critical` | Label here | `source_file.md > Section Name`
- PRD-002 | `important` | Label here | `source_file.md > Section Name`
```

After all areas, include a summary line:

```
Total: N requirements (X critical, Y important, Z detail) across M functional areas
```

### 2. Coverage Table

Evaluate the plan against each requirement you extracted.

Output a markdown table with exactly these columns:

| PRD-ID | Requirement | Severity | Coverage | Evidence | Gap |
| ------ | ----------- | -------- | -------- | -------- | --- |

- **PRD-ID**: The ID from your extraction above.
- **Requirement**: The short label (8–12 words).
- **Severity**: The severity tier from your extraction (`critical`, `important`, or `detail`).
- **Coverage**: Exactly one of: `full`, `partial`, `missing`.
  - `full` — The plan explicitly addresses this with a concrete task or section. You can point to it.
  - `partial` — The plan touches on it but lacks specificity, only addresses part of it, or handles it by implication rather than directly.
  - `missing` — The plan does not address this at all.
- **Evidence**: Cite the specific section name, task ID, or short quote from the plan that addresses it. If `missing`, write `none`.
- **Gap**: If `partial` or `missing`, one sentence explaining what's lacking. If `full`, leave blank.

### 3. Coverage Scores

Calculate scores at three levels. Show the math for each.

**Overall score:**

```
score = (full_count × 1.0 + partial_count × 0.5) / total_count × 100
```

**Score by severity tier:**

Calculate the same formula separately for `critical`, `important`, and `detail` requirements. Display as:

```
Critical:  (full × 1.0 + partial × 0.5) / critical_total × 100 = ___%  (n of N critical requirements)
Important: (full × 1.0 + partial × 0.5) / important_total × 100 = ___%  (n of N important requirements)
Detail:    (full × 1.0 + partial × 0.5) / detail_total × 100 = ___%  (n of N detail requirements)
Overall:   ___% (N total requirements)
```

### 4. Top Gaps

List the **top 5 gaps** (or fewer if there aren't 5), ranked by severity tier first (critical before important before detail), then by impact within tier. For each gap:

- State the PRD-ID, severity, and requirement label.
- Explain why this gap matters (what breaks or degrades without it).

### 5. Coverage Narrative

Write 3–5 paragraphs that a human reader can use to understand the coverage posture without reading the table. This section must answer these questions:

1. **Overall posture**: In plain language, how ready is this plan? Is it a strong plan with minor gaps, a structurally sound plan with concerning holes, or a plan that misses fundamental requirements?

2. **Strength clusters**: Where is the plan strongest? Which areas of the PRD are thoroughly and concretely covered? Name the functional areas (not just PRD-IDs) — e.g., "data persistence architecture," "search and catalog integration," "developer experience tooling."

3. **Weakness clusters**: Where do gaps concentrate? Are the partial/missing items scattered randomly, or do they cluster around a specific functional area, a specific type of requirement (behavioral specs vs. architectural constraints vs. UX details), or a specific severity tier? Name the pattern.

4. **Risk assessment**: If this plan were executed as-is without addressing any gaps, what is the most likely failure mode? What would a user, stakeholder, or QA reviewer notice first? Be specific — name the scenario, not just the requirement.

5. **Remediation guidance**: For the weakness clusters you identified, what category of work is needed — more detailed specification, architectural decisions, missing acceptance criteria, or entirely new plan sections? This is not a task list — it is guidance on what *kind* of planning work remains.

## Hard Rules

- Be honest. Do not inflate coverage. If something is only covered by implication or vague language, it is `partial`, not `full`.
- Do not invent requirements that aren't in the PRD. Every requirement must have a citable source location.
- Do not skip requirements because they seem minor — include every requirement in the table and let the scoring formula do its job.
- Use the exact table formats above. Do not add, remove, or rename columns.
- Use exactly the words `full`, `partial`, or `missing` for Coverage — no other labels.
- Use exactly the words `critical`, `important`, or `detail` for Severity — no other labels.
- The anchor rule is binding. If you cannot cite a specific PRD file and section for a requirement, delete it from your list.
- The merge and split rules are binding. Same testable outcome stated twice = one requirement. Multiple testable behaviors in one statement = multiple requirements.
- The Coverage Narrative must not merely restate the table data. It must synthesize and interpret. Saying "6 items are partial" is restating; saying "the partial items cluster around AI behavioral contracts, suggesting the plan treats AI as an implementation detail rather than a specifiable surface" is interpreting.

## Output Location

Write the full evaluation to: `results/PLAN_EVAL.md`
