# Upgrade Instructions

These instructions are read by the core `upgrade` skill when upgrading this hermit.

## v0.0.1 → v0.0.2

Terminology alignment with core v0.0.2:

1. In project's CLAUDE.md dev block (`<!-- claude-code-dev-hermit: Development Workflow -->`):
   - Replace the entire block with the updated `state-templates/CLAUDE-APPEND.md`
   - Key changes: `ACTIVE.md` → `SHELL.md`, `NEXT-MISSION.md` → `NEXT-TASK.md`, `mission` → `task`, `Steps` → `Plan`

No config changes required — this release is documentation/terminology only.
