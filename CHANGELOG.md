# Changelog

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
