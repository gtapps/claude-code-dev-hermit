# Changelog

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
