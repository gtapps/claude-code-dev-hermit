# claude-code-dev-hermit

Software development agents, workflows, and git safety for claude-code-hermit.

## This Repo is a Plugin

This repo is a Claude Code plugin. It extends `claude-code-hermit` (core v0.0.1+) with software development capabilities.

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

- `claude-code-hermit` v0.0.1+ (core) — session discipline, cost tracking, OPERATOR.md, learning loop

## Core Contracts

This dev pack honours these core contracts:

1. Profile-gating via `AGENT_HOOK_PROFILE` env var (`minimal`/`standard`/`strict`)
2. Session lifecycle: `session-start` → work → `session-close`
3. State dir: `.claude/.claude-code-hermit/` (sessions/, proposals/, templates/)
4. Domain pack detection via glob on plugin.json
5. Pack activation appends `state-templates/CLAUDE-APPEND.md` to CLAUDE.md
6. `strict` profile = "all of standard + domain pack hooks"
7. Learning loop: dev pack provides dev-specific proposal categories but does NOT invoke `pattern-detect` (core handles it at session-close)
8. NEXT-MISSION.md: dev-session is aware core handles this at session-start; does not duplicate
9. Dev proposal categories: `[missing-tests]`, `[tech-debt]`, `[dependency]`, `[tooling]`, `[architecture]` complement core auto-detection categories
10. Always-on lifecycle: dev-session aware of idle transition vs full shutdown; dev close checklist qualifies items by close mode
11. Session hygiene: dev workflows note ACTIVE.md compaction responsibility after implementer cycles
