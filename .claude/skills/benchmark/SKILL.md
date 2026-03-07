---
name: benchmark
description: "Create and manage benchmark worktrees. Use when user says: new benchmark, benchmark new, bench new, or asks to create a new benchmark run."
argument-hint: new [model tool effort variant]
---

# Benchmark Skill

Manage benchmark worktrees for evaluating coding agent planning quality.

## Commands

| Command | What it does |
|---------|-------------|
| **new** | Interview the user, then create a new benchmark worktree |

## Routing

| Input | Command file |
|-------|-------------|
| `new`, `benchmark new`, `new benchmark`, `bench new` | `commands/new.md` |

## Context

- **Benchmarker repo:** The repo this skill lives in (the current working directory)
- **Worktree root:** `~/code/shows_tests/` — all worktrees live here
- **Naming convention:** `{model}__{tool}[__{effort}][__{variant}]` using BEM-style double underscores, all snake_case, no dashes or dots
