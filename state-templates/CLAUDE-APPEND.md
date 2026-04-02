
---
<!-- claude-code-dev-hermit: Development Workflow -->

## Dev Agent (claude-code-dev-hermit)

| Agent | When to use | Model |
|-------|------------|-------|
| `implementer` | Writing code, bug fixes, refactoring (worktree isolated) | Sonnet |

## Git Safety

- Never push to main or master directly
- Never use `--no-verify` on git commands
- Always create feature branches for work

The `implementer` agent enforces these via "Forbidden Actions" prompt rules (always active).
The `git-push-guard` hook provides a second enforcement layer at **strict** profile only.
Set `env.AGENT_HOOK_PROFILE` to `"strict"` in `.claude-code-hermit/config.json` to enable hook enforcement.

## Technical Constraints

Subagents cannot invoke skills (/simplify, /batch, etc.) — those must run in the main session only.

## Dev Workflow

When doing development work:

1. **Plan**: break the task into steps, create a Task for each (skip TaskCreate for trivial single-step tasks)
2. **Implement**: use the `implementer` agent for code changes (worktree-isolated feature branches)
3. **After implementer returns**: update SHELL.md — log branch name in Progress Log, append changed files to Changed section, note any test failures or concerns in Blockers
4. **Quality pass**: run `/claude-code-dev-hermit:dev-quality`
5. **If critical issues**: loop back to implementation before proceeding
6. **Task boundary**: invoke `reflect`, serialize and delete all Tasks, then idle-transition via `session-mgr`

First session (no prior `S-*-REPORT.md` files): explore the codebase to orient yourself before starting work.

### Parallel Work

- Same change across many files: use `/batch`
- Independent tasks in different areas: use Agent Teams (if `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`), otherwise implement sequentially
- When unsure: ask the operator
- After parallel work completes: run `/simplify` and code review in the main session (workers can't invoke skills)
- `/batch` workers don't have access to OPERATOR.md — enforce constraints via post-batch review

### Before Archiving a Task

- Feature branch is committed (no uncommitted changes)
- Tests pass on the feature branch
- If partial implementation: Session Summary describes what was completed and what remains

## Dev Session Hygiene

- **Tasks**: plan steps are tracked as Tasks (TaskCreate/TaskUpdate) — do not maintain a Plan table in SHELL.md. Skip TaskCreate for trivial single-step tasks.
- **Progress Log**: if entries exceed 50, summarize older entries into a compact block; keep last 10 in detail

## Dev Proposal Categories

Use these prefixes in proposal titles for consistent sorting:
- **[missing-tests]** — Uncovered code paths
- **[tech-debt]** — Code that works but should be refactored
- **[dependency]** — Stale, vulnerable, or unnecessary deps
- **[tooling]** — Missing linter rules, CI checks, dev scripts
- **[architecture]** — Structural improvements

All dev rules (git safety, checklist, categories) apply equally to autonomous idle work.

## Dev Quick Reference

- Quality pass: `/claude-code-dev-hermit:dev-quality`
- Branch cleanup: `/claude-code-dev-hermit:dev-cleanup`
- List proposals: `/claude-code-hermit:proposal-list` (includes auto-detected)
- Act on proposal: `/claude-code-hermit:proposal-act accept|defer|dismiss PROP-NNN`
