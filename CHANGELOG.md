# Changelog

## [0.1.1] - 2026-04-02

### Fixed
- `skills/dev-quality/SKILL.md`: step 4 still referenced deprecated `/review` — replaced with `code-review` plugin
- `state-templates/CLAUDE-APPEND.md`: removed stale `/review` references from Technical Constraints and Parallel Work sections
- `state-templates/CLAUDE-APPEND.md`: removed Dev Session Hygiene bullets that duplicated core's CLAUDE-APPEND (Monitoring/Summary compaction, Cost section)
- `state-templates/CLAUDE-APPEND.md`: collapsed verbose Technical Constraints (3 lines → 1)

## [0.1.0] - 2026-04-02

### Removed
- `agents/reviewer.md` — dead code, never invoked by any workflow. Code review handled by `code-review` plugin
- `agents/repo-mapper.md` — replaced by Claude Code's built-in Explore subagent and auto memory
- `skills/dev-session/SKILL.md` — workflow orchestration collapsed into CLAUDE-APPEND.md behavioral instructions
- `skills/dev-parallel/SKILL.md` — thin routing collapsed into CLAUDE-APPEND.md (5 lines replace 73)
- `state-templates/dev-checklist.md` — merged into CLAUDE-APPEND.md
- `state-templates/dev-reference.md` — merged into CLAUDE-APPEND.md
- `state-templates/OPERATOR-QUESTIONS.md` — inlined into dev-hatch skill

### Changed
- `skills/init` renamed to `skills/dev-hatch` — follows core's naming convention (`hatch` = one-time setup), uses `AskUserQuestion` wizard with auto-detection like core's hatch skill
- `state-templates/CLAUDE-APPEND.md`: rebuilt as self-contained dev workflow document (~70 lines). Includes dev workflow steps, parallel work heuristic, task completion checklist, proposal categories, and session hygiene — all previously spread across 4 files and 2 skills
- `skills/dev-quality/SKILL.md`: simplified — removed fragile batch-skip conditional (/simplify is idempotent), removed dead reviewer agent mention, replaced deprecated built-in `/review` with `code-review` plugin
- `skills/dev-hatch/SKILL.md` (was init): added `code-review@claude-plugins-official` to recommended companion plugins, inlined operator questions with auto-detection, version stamping, updated report to reflect simplified architecture (1 agent, 2 skills)
- `CLAUDE.md`: reduced core contracts from 12 to 5 essential behavioral contracts, removed `/review` from built-in skills (deprecated), updated plugin structure
- `.claude-plugin/plugin.json`: version 0.1.0, updated description
- `README.md`: updated to reflect simplified architecture

### Why
- Philosophy: leverage what Claude Code natively offers, don't reinvent, don't overbuild
- `/simplify`, `/batch`, `/debug` are built-in bundled skills — no need to orchestrate them via custom skills
- `/review` is officially deprecated — replaced by `code-review` plugin
- Claude Code's Explore subagent replaces repo-mapper; auto memory replaces one-time orientation scans
- TaskCreate/TaskUpdate, Plan mode, and CLAUDE.md instructions handle workflow sequencing natively
- Net result: 14 files → 7 files, ~36% reduction with zero capability loss

## [0.0.9] - 2026-03-31

### Changed
- Aligned with core v0.2.1 (mandatory proposal pipeline)
- `skills/dev-session/SKILL.md`: idle transition step 6 now serializes and deletes all tasks (was: TaskUpdate to completed/cancelled). Matches core v0.2.0 "clean slate" behavior — serialized table passed to session-mgr for archive.
- `state-templates/CLAUDE-APPEND.md`: Dev Session Hygiene notes "skip TaskCreate for quick single-step tasks" (aligns with core v0.2.0 policy)
- `state-templates/dev-reference.md`: Dev Proposal Categories now notes that dev improvements must go through PROP-NNN pipeline before implementation (core v0.2.1 enforcement)

## [0.0.8] - 2026-03-31

### Changed
- Aligned with core v0.2.0 (Task API replaces Plan table in SHELL.md)
- `skills/dev-session/SKILL.md`: task planning now uses `TaskCreate` per step and `TaskUpdate` for progress; "after implementer returns" updates task status via `TaskUpdate`; idle transition calls `TaskUpdate` before session-mgr archive; removed External Dependencies section
- `skills/dev-parallel/SKILL.md`: parallel item tracking uses `TaskUpdate` instead of SHELL.md Plan section; removed External Dependencies and /batch fallback-to-sequential (built-in)
- `skills/dev-quality/SKILL.md`: new skill — extracted quality pass (tests → /simplify → tests → /review) from dev-session; dev-session now calls it as a single step
- `state-templates/CLAUDE-APPEND.md`: slimmed from 104 to ~57 lines; Dev Session Hygiene updated to reference Tasks for plan tracking; Idle Agency and Dev Proposal Categories moved to `dev-reference.md`; Dev Task Completion Checklist moved to `dev-checklist.md`
- `state-templates/dev-checklist.md`: new file — Dev Task Completion Checklist (loaded on demand at task completion)
- `state-templates/dev-reference.md`: new file — Idle Agency Awareness and Dev Proposal Categories (loaded on demand)
- `skills/init/SKILL.md`: prerequisite check now requires core v0.2.0+ and verifies `CLAUDE_CODE_TASK_LIST_ID` in `.claude/settings.local.json`
- `agents/reviewer.md`: removed speculative "Review Interpreter" future direction comment
- `scripts/git-push-guard.test.js`: new file — 18-case test suite for git-push-guard.js
- `CLAUDE.md`: "External Dependencies" replaced with "Built-in Claude Code Skills Used"; dependency bumped to v0.2.0+; contract 7 step reference removed
- `README.md`: "Optional Dependencies" replaced with "Built-in Claude Code Skills" table; added Context7 as recommended MCP companion; version requirement updated to v0.2.0+

## [0.0.7] - 2026-03-30

### Changed
- Aligned with core v0.1.1 (config-driven compaction, cost tracking, session-mgr changes)
- `state-templates/CLAUDE-APPEND.md`: "Dev Session Hygiene" rewritten — Progress Log compaction is dev-session's job during active work; Monitoring/Session Summary compaction deferred to session-mgr on idle transition; notes `## Cost` is auto-populated
- `skills/dev-session/SKILL.md`: post-implementer hygiene note updated with split compaction responsibilities; idle transition step 6 notes session-mgr compaction
- `skills/init/SKILL.md`: prerequisite check warns if core version < 0.1.1
- `CLAUDE.md`: dependency bumped to v0.1.1+, contract 11 updated to reference core's compact system
- `.claude-plugin/marketplace.json`: version aligned with plugin.json (was stuck at 0.0.5)

## [0.0.6] - 2026-03-30

### Changed
- Aligned with core v0.0.6–v0.0.10
- `init` skill: reads/writes `env.AGENT_HOOK_PROFILE` from `.claude-code-hermit/config.json` instead of `.claude/settings.json` (core v0.0.7 env system alignment)
- `state-templates/CLAUDE-APPEND.md`: "Idle Agency Awareness" section updated to reference `idle_behavior` config key (`"wait"`/`"discover"`) and `## When Idle` OPERATOR.md section (core v0.0.9 routines/idle_behavior alignment)
- `UPGRADE.md`: added v0.0.5→v0.0.6 migration instructions covering env system, idle_behavior, and YAML frontmatter
- Version requirement bumped to `claude-code-hermit v0.0.10+`

## [0.0.5] - 2026-03-28

### Changed
- Aligned with core v0.0.5: `pattern-detect` → `reflect` rename throughout
- All references to `pattern-detect` updated to `reflect` in CLAUDE.md contracts, CLAUDE-APPEND.md, dev-session skill, and init skill
- Version requirement bumped to claude-code-hermit v0.0.5+
- UPGRADE.md updated with v0.0.4→v0.0.5 migration instructions

## [0.0.4] - 2026-03-27

### Changed
- Aligned with core v0.0.4: memory-driven reflection, idle agency, daily rhythm
- Pattern-detect invocation at task boundaries reframed: supplementary reflection point (platform now learns independently via heartbeat and natural pauses)
- Removed 4 deterministic auto-detection categories ([blocker], [workaround], [cost-trend], [tag-correlation]) — reflection is freeform
- Removed 3-report prerequisite for pattern-detect — learns from day one
- Dev proposal categories reframed as a filing system, not a structural counterpart to core categories
- CLAUDE-APPEND: added Idle Agency Awareness section — dev rules apply to all dev work including autonomous idle pickup
- Contract 7: learning invocation framed as supplementary to platform, not sole trigger
- Contract 9: dev categories reframed as filing system
- Added Contract 12: ambient dev rules — behavioral rules apply regardless of how dev work was initiated
- init skill: pattern-detect description updated to show all trigger sources
- Version requirement bumped to claude-code-hermit v0.0.4+

## [0.0.3] - 2026-03-26

### Changed
- Unified session lifecycle alignment with core v0.0.3
- dev-session restructured as a persistent task loop: quality pass → idle transition → "What's next?" → next task
- dev-session no longer invokes `/session-close` — operator runs it when they want full shutdown
- dev-session now invokes `pattern-detect` directly at task boundaries (core's `/session-close` no longer runs at task boundaries)
- "Dev Session Close Checklist" renamed to "Dev Task Completion Checklist" (verified at every task boundary, not just close)
- dev-parallel notes that parallel work completion feeds back into dev-session's task-complete flow
- Removed all close mode branching, "always-on only" qualifiers, and idle/shutdown mode distinctions
- Version requirement bumped to claude-code-hermit v0.0.3+

## [0.0.2] - 2026-03-25

### Changed
- Terminology alignment with core v0.0.2: `ACTIVE.md` → `SHELL.md`, `NEXT-MISSION.md` → `NEXT-TASK.md`, `mission` → `task`, `Steps` → `Plan`, `domain pack` → `hermit`
- Version requirement bumped to claude-code-hermit v0.0.2+
- UPGRADE.md updated with v0.0.1→v0.0.2 migration instructions

## [0.0.1] - 2026-03-25

### Added
- Initial release: 3 agents (repo-mapper, implementer, reviewer), 4 skills (init, dev-session, dev-parallel, dev-cleanup), 1 hook (git-push-guard)
- State templates: CLAUDE-APPEND.md, OPERATOR-QUESTIONS.md
- Git safety enforcement at strict profile
