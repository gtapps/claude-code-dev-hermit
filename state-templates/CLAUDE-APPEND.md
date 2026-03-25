
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

## Dev Session Close Checklist

Before closing a dev session, verify these IN ADDITION to core's
quality checklist:

- [ ] Feature branch is committed (no uncommitted changes in worktree)
- [ ] Tests pass on the feature branch
- [ ] Branch name is recorded in ACTIVE.md
- [ ] If implementation is partial (full shutdown only): "Next Start Point" describes what
      remains to implement (not just "continue work") — idle transitions use Session Summary instead
- [ ] If reviewer was invoked: recommendation (approve/request-changes/
      discuss) is recorded in Progress Log
- [ ] If /simplify was run: note whether it was applied or reverted
- [ ] Pattern detection: core's session-close runs pattern-detect automatically —
      no manual step needed, but verify any auto-created proposals make sense

## Dev Session Hygiene

Dev workflows generate verbose ACTIVE.md updates (implementer output, review findings,
/simplify results). Core's session hygiene rules apply: keep ACTIVE.md under 150 lines,
compact at 200+. After each implementer cycle, check line count and summarize older
Progress Log entries if needed.

## Dev Proposal Categories

When creating proposals during dev sessions, use these categories in
the proposal title prefix for consistent sorting:

- **[missing-tests]** — Uncovered code paths or missing edge case tests
- **[tech-debt]** — Code that works but should be refactored
- **[dependency]** — Stale, vulnerable, or unnecessary dependencies
- **[tooling]** — Missing linter rules, CI checks, or dev scripts
- **[architecture]** — Structural improvements worth discussing

Example: `PROP-014: [tech-debt] Extract payment logic into service layer`

Auto-detected proposals (created by core's pattern-detect at session close) use
core categories: [blocker], [workaround], [cost-trend], [tag-correlation].
Dev categories above are for manual proposals created during dev sessions.

## Dev Quick Reference

- Dev session: `/claude-code-dev-hermit:dev-session`
- Parallel work: `/claude-code-dev-hermit:dev-parallel`
- Branch cleanup: `/claude-code-dev-hermit:dev-cleanup`
- List proposals: `/claude-code-hermit:proposal-list` (includes auto-detected)
- Act on proposal: `/claude-code-hermit:proposal-act accept|defer|dismiss PROP-NNN`
- Next mission: accepted proposals may create NEXT-MISSION.md, offered at session start
