# shows PRD experiment (starter template)

This repo is a baseline "starter" used to compare builds produced by different model releases over time.

The intent is:
- Keep `main` as the stable baseline.
- For each model/run, create a **new branch in a separate git worktree**, let the model implement changes, then commit on that branch.

## First-Time Setup

If this directory is not a git repo yet:

```bash
git init
git switch -c main
```

## What's In Here

- `docs/shows_prd/START_HERE.md`: entry point to the product spec (PRD + supporting docs).
- `AGENTS.md`: development guidelines (architecture + code standards). Treat this as a hard constraint for implementations.
- `.vscode/settings.json`: minimal editor preferences.

## Recommended Workflow (Git Worktrees)

1. Ensure `main` is up to date:

```bash
git switch main
git pull --ff-only
```

2. Create a new worktree + branch for the model/run:

```bash
# Example naming, adjust as you like
BRANCH="exp/gpt-5_2026-02-07"
DIR="../shows_prd-${BRANCH//\//_}"

git worktree add "$DIR" -b "$BRANCH"
cd "$DIR"
```

3. Run the build with the new model, commit changes to that branch, and push.

4. When done, remove the worktree:

```bash
cd -
git worktree remove "$DIR"
```

## Branch Naming

Pick a convention that makes diffs and history easy to scan. One workable pattern:

- `exp/<model>_<yyyy-mm-dd>` (optionally add a short suffix like `_v1`).

## Notes

- Don't commit secrets. Use `.env*` files locally; keep an `.env.example` if needed.
- When code lands, follow the fractal structure described in `AGENTS.md` (Pages -> Features -> Sub-Features).
