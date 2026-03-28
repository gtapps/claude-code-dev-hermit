
---
<!-- claude-code-dev-hermit: Development Workflow -->

## Dev Subagents (claude-code-dev-hermit)

| Agent | When to use | Model |
|-------|------------|-------|
| `repo-mapper` | Orientation, file discovery, dependency mapping | Haiku |
| `implementer` | Writing code, bug fixes, refactoring (worktree isolated) | Sonnet |
| `reviewer` | Code review, validation, quality checks (read-only) | Sonnet |

## Git Safety

- Never push to main or master directly
- Never use `--no-verify` on git commands
- Always create feature branches for work

The `implementer` agent enforces these via "Forbidden Actions" prompt rules (always active).
The `git-push-guard` hook provides a second enforcement layer at **strict** profile only.
Set `AGENT_HOOK_PROFILE=strict` in `.claude/settings.json` to enable hook enforcement.

## Technical Constraints

Subagents (repo-mapper, implementer, reviewer) cannot invoke skills like
/simplify, /review, or /batch. These skills must be invoked from the main
session only. The dev-session workflow handles this automatically —
/simplify and /review run in the main session after the implementer
agent returns, not inside the implementer.

Do NOT add /simplify, /review, /batch, or /debug invocations to agent
definition files (agents/\*.md). They will silently fail because subagents
have access to tools (Read, Bash, Grep, etc.) but not the Skill tool.

## Dev Task Completion Checklist

Before archiving a task (idle transition), verify these IN ADDITION to core's
quality checklist:

- [ ] Feature branch is committed (no uncommitted changes in worktree)
- [ ] Tests pass on the feature branch
- [ ] Branch name is recorded in SHELL.md
- [ ] If implementation is partial: Session Summary describes what was completed
      and what remains
- [ ] If reviewer was invoked: recommendation (approve/request-changes/
      discuss) is recorded in Progress Log
- [ ] If /simplify was run: note whether it was applied or reverted
- [ ] Pattern detection: dev-session invokes reflect at every task
      boundary — verify any auto-created proposals make sense

## Dev Session Hygiene

Dev workflows generate verbose SHELL.md updates (implementer output, review findings,
/simplify results). Core's session hygiene rules apply: keep SHELL.md under 150 lines,
compact at 200+. After each implementer cycle, check line count and summarize older
Progress Log entries if needed.

## Idle Agency Awareness

During idle, heartbeat may autonomously pick up work — including dev tasks
from NEXT-TASK.md (created by accepted dev proposals). All dev rules apply
to autonomous work the same as operator-directed work:

- Git safety (no push to main, no --no-verify, feature branches only)
- Dev Task Completion Checklist (verified at every task boundary)
- Dev proposal categories (for any manual proposals created during the work)

Idle agency is gated by escalation level. The hermit asks before taking
high-impact actions at conservative/balanced escalation.

## Dev Proposal Categories

When creating proposals during dev sessions, use these categories in
the proposal title prefix for consistent sorting:

- **[missing-tests]** — Uncovered code paths or missing edge case tests
- **[tech-debt]** — Code that works but should be refactored
- **[dependency]** — Stale, vulnerable, or unnecessary dependencies
- **[tooling]** — Missing linter rules, CI checks, or dev scripts
- **[architecture]** — Structural improvements worth discussing

Example: `PROP-014: [tech-debt] Extract payment logic into service layer`

Auto-detected proposals come from reflect's reflection on memory
and recent work — no fixed categories. They may surface dev-relevant
patterns (recurring test failures, workarounds, cost anomalies) alongside
broader operational patterns. Dev categories above are a filing system for
proposals you create explicitly during dev sessions.

## Dev Quick Reference

- Dev session: `/claude-code-dev-hermit:dev-session`
- Parallel work: `/claude-code-dev-hermit:dev-parallel`
- Branch cleanup: `/claude-code-dev-hermit:dev-cleanup`
- List proposals: `/claude-code-hermit:proposal-list` (includes auto-detected)
- Act on proposal: `/claude-code-hermit:proposal-act accept|defer|dismiss PROP-NNN`
- Next task: accepted proposals may create NEXT-TASK.md, offered at session start
