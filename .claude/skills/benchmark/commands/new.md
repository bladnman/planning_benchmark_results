# New Benchmark

Create a new benchmark worktree for evaluating a model's planning ability.

**Argument**: $ARGUMENTS

## Goal

Interview the user for benchmark parameters, construct a worktree/branch name, verify it doesn't exist, create the worktree, and open it in Cursor.

## Naming Convention

Names follow BEM-style with snake_case and double-underscore separators:

```
{model}__{tool}[__{effort}][__{variant}]
```

- **model** — model family + version + tier (required)
- **tool** — development environment / CLI used (required)
- **effort** — thinking/compute effort level (optional)
- **variant** — experiment tweak (optional)

No dashes. No dots. All lowercase snake_case.

## Step 1: Interview

If `$ARGUMENTS` provides all needed info, skip to confirmation. Otherwise, ask the user for each parameter interactively.

### Model name

Ask: "What model are you benchmarking?"

Show examples from known models:
```
gemini_3_2_pro       opus_4_5          sonnet_4_5
gemini_3_2_flash     opus_4_6          sonnet_4_6
gpt_5_3_codex        gpt_5_2_codex     gpt_o3
```

Accept freeform input. Normalize to snake_case (e.g., "Gemini 3.2 Pro" -> `gemini_3_2_pro`).

### Tool name

Ask: "What tool/environment are you using?"

Show examples:
```
claude_code      cursor          codex_cli
gemini_cli       antigravity     windsurf
aider            cline           copilot
```

Accept freeform input. Normalize to snake_case.

### Effort level (optional)

Ask: "Any specific effort/thinking level? (press enter to skip)"

Show examples:
```
xhigh    high    med    low
```

### Variant (optional)

Ask: "Any experiment variant? e.g., no_plan, no_eval, custom_prompt (press enter to skip)"

## Step 2: Construct & Confirm

Build the name from the parts:
```
{model}__{tool}                          # base
{model}__{tool}__{effort}                # with effort
{model}__{tool}__{effort}__{variant}     # with both
{model}__{tool}__{variant}               # effort skipped, variant present
```

Show the user:
```
Worktree name:  gemini_3_2_pro__cursor
Branch name:    gemini_3_2_pro__cursor
Location:       ~/code/shows_tests/gemini_3_2_pro__cursor
```

Ask for confirmation before proceeding.

## Step 3: Verify Uniqueness

Check that neither the folder nor the branch already exists:

```bash
# Check folder
ls -d ~/code/shows_tests/{name} 2>/dev/null

# Check branch (local and remote)
git branch --list {name}
git ls-remote --heads origin {name}
```

If either exists, tell the user and ask them to adjust.

## Step 4: Create Worktree

```bash
git worktree add ~/code/shows_tests/{name} -b {name}
```

This creates the worktree from the current HEAD of main, with a new branch of the same name.

## Step 5: Open in Cursor

```bash
cursor ~/code/shows_tests/{name}
```

## Step 6: Summary

Tell the user:
```
Benchmark worktree created!

  Name:     {name}
  Branch:   {name}
  Location: ~/code/shows_tests/{name}

  Cursor is opening. In that window:
  1. Run Step 1: Tell the agent to read 1-START_HERE.md
  2. Run Step 2: Fresh context, read 2-EVALUATE_PLAN.md
  3. Run Step 3: Fresh context, read 3-PLAN_EVAL_REPORT.md

  When done, come back here and push the results.
```

## Safety

- Never overwrite an existing worktree or branch
- Always confirm the name with the user before creating
- If `cursor` command is not available, fall back to printing the path
