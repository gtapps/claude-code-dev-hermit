# claude-code-dev-hermit

Software development agents, workflows, and git safety for claude-code-hermit.

## This Repo is a Plugin

This repo is a Claude Code plugin. It extends `claude-code-hermit` (core v0.0.5+) with software development capabilities.

Install flow for target projects:

```
claude plugin marketplace add gtapps/claude-code-hermit
claude plugin install claude-code-hermit@claude-code-hermit --scope project
/claude-code-hermit:init

claude plugin marketplace add gtapps/claude-code-dev-hermit
claude plugin install claude-code-dev-hermit@claude-code-dev-hermit --scope project
/claude-code-dev-hermit:init
```

## Plugin Structure

- `agents/` — dev subagent definitions (repo-mapper, implementer, reviewer)
- `skills/` — dev skills (init, dev-session, dev-parallel, dev-cleanup)
- `hooks/hooks.json` — git-push-guard hook (strict profile only)
- `scripts/` — hook scripts
- `state-templates/` — dev-specific CLAUDE.md additions
- `.claude-plugin/plugin.json` — plugin manifest
- `.claude-plugin/marketplace.json` — marketplace metadata

## Hook Profiles

The `git-push-guard` hook activates at **strict** profile only (`AGENT_HOOK_PROFILE=strict`).
The init skill recommends enabling strict profile during setup.

## External Dependencies

These optional skills enhance the workflow:

- `/simplify` — code cleanup (skipped if unavailable)
- `/pr-review-toolkit:review-pr` — 6-agent review (falls back to `reviewer` agent)
- `/batch` — parallel pattern execution (falls back to sequential `implementer`)

## Depends On

- `claude-code-hermit` v0.0.5+ (core) — session discipline, cost tracking, OPERATOR.md, learning loop

## Core Contracts

This dev hermit honours these core contracts:

1. Profile-gating via `AGENT_HOOK_PROFILE` env var (`minimal`/`standard`/`strict`)
2. Session lifecycle: `session-start` → work → idle → work → ... → `session-close` (operator-invoked). Dev-session owns the task loop; `/session-close` is never called at task boundaries
3. State dir: `.claude/.claude-code-hermit/` (sessions/, proposals/, templates/)
4. Hermit detection via glob on plugin.json
5. Hermit activation appends `state-templates/CLAUDE-APPEND.md` to CLAUDE.md
6. `strict` profile = "all of standard + hermit hooks"
7. Learning loop: dev hermit provides dev-specific proposal categories and invokes `reflect` at every task boundary (idle transition step 5, before archive) to ensure task completion is a reflection point. Core's reflect also fires independently (heartbeat, natural pauses, end of day) — dev-session's invocation supplements the platform, not replaces it. `/session-close` remains operator-invoked shutdown only
8. NEXT-TASK.md: dev-session is aware core handles this at session-start; does not duplicate
9. Dev proposal categories: `[missing-tests]`, `[tech-debt]`, `[dependency]`, `[tooling]`, `[architecture]` are a filing system for dev-specific proposals (manual and auto-detected). Core's reflection may independently surface dev-relevant patterns — no fixed categories
10. Unified lifecycle: dev-session owns the task loop — performs idle transition at every task boundary (mirrors core session skill step 6), never invokes session-close directly
11. Session hygiene: dev workflows note SHELL.md compaction responsibility after implementer cycles
12. Ambient dev rules: dev-hermit's behavioral rules (git safety, task completion checklist, proposal categories) apply to all dev work regardless of how it was initiated — operator-directed `/dev-session`, NEXT-TASK.md pickup during idle, or autonomous idle agency work
