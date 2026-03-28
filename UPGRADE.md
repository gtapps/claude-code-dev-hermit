# Upgrade Instructions

These instructions are read by the core `upgrade` skill when upgrading this hermit.

## v0.0.4 → v0.0.5

Alignment with core v0.0.5: `pattern-detect` → `reflect` rename, Docker-first always-on, takeover/hand-back.

1. Run `/claude-code-hermit:upgrade` to update core's CLAUDE-APPEND block first (adds Docker setup, takeover/hand-back skills, conversational auto-triggers)
2. In project's CLAUDE.md dev block (`<!-- claude-code-dev-hermit: Development Workflow -->`):
   - Replace the entire block with the updated `state-templates/CLAUDE-APPEND.md`
   - Key changes: all `pattern-detect` references → `reflect`
3. In project's CLAUDE.md core contracts section:
   - Contract 7: `pattern-detect` → `reflect`

No config changes required. Core's new Docker/takeover skills are core-only — no dev-hermit wrapping needed.

## v0.0.3 → v0.0.4

Alignment with core v0.0.4: memory-driven learning, idle agency, reflection-based pattern detection.

1. Run `/claude-code-hermit:upgrade` to update core's CLAUDE-APPEND block first (adds Self-Awareness, Idle Behavior, Daily Rhythm, Learning Model sections)
2. In project's CLAUDE.md dev block (`<!-- claude-code-dev-hermit: Development Workflow -->`):
   - Replace the entire block with the updated `state-templates/CLAUDE-APPEND.md`
   - Key changes: removed 4 auto-detection categories, added Idle Agency Awareness section, reframed dev categories as filing system
3. In project's CLAUDE.md core contracts section:
   - Contract 7: learning invocation is now supplementary (platform learns independently)
   - Contract 9: dev categories are a filing system, not a structural counterpart
   - New Contract 12: dev rules apply to all dev work including autonomous idle pickup

No config changes required. Core's new config keys (morning_routine, evening_routine, idle_agency) are handled by core's upgrade skill.

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
