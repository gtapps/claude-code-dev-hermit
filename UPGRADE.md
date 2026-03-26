# Upgrade Instructions

These instructions are read by the core `upgrade` skill when upgrading this hermit.

## v0.0.2 → v0.0.3

Unified session lifecycle alignment with core v0.0.3:

1. Run `/claude-code-hermit:upgrade` to update core's CLAUDE-APPEND block first
2. In project's CLAUDE.md dev block (`<!-- claude-code-dev-hermit: Development Workflow -->`):
   - Replace the entire block with the updated `state-templates/CLAUDE-APPEND.md`
   - Key changes: "Dev Session Close Checklist" → "Dev Task Completion Checklist", no more idle/shutdown mode distinction
   - Both blocks (core + dev) should be updated in the same pass

No config changes required — this release aligns dev-session behavior with core's unified lifecycle.

## v0.0.1 → v0.0.2

Terminology alignment with core v0.0.2:

1. In project's CLAUDE.md dev block (`<!-- claude-code-dev-hermit: Development Workflow -->`):
   - Replace the entire block with the updated `state-templates/CLAUDE-APPEND.md`
   - Key changes: `ACTIVE.md` → `SHELL.md`, `NEXT-MISSION.md` → `NEXT-TASK.md`, `mission` → `task`, `Steps` → `Plan`

No config changes required — this release is documentation/terminology only.
