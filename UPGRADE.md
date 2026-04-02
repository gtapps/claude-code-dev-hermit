# Upgrade Instructions

These instructions are read by the core `hermit-upgrade` skill when upgrading this hermit.

## v0.0.9 → v0.1.0

Major simplification: removed overbuilt components, collapsed workflow orchestration into CLAUDE.md instructions.

1. Run `/claude-code-hermit:hermit-upgrade` to update core first
2. In project's CLAUDE.md dev block (`<!-- claude-code-dev-hermit: Development Workflow -->`):
   - Remove the entire existing dev block
   - Re-run `/claude-code-dev-hermit:dev-hatch` to append the new simplified block
   - Or manually replace with the updated `state-templates/CLAUDE-APPEND.md`
3. Install the `code-review` plugin (replaces deprecated built-in `/review`):
   ```
   claude plugin install code-review@claude-plugins-official --scope project
   ```
4. The following are removed and no longer needed:
   - `/claude-code-dev-hermit:dev-session` — workflow rules are now in CLAUDE.md
   - `/claude-code-dev-hermit:dev-parallel` — routing heuristic is now in CLAUDE.md
   - `repo-mapper` agent — use Claude Code's built-in Explore subagent
   - `reviewer` agent — use the `code-review` plugin
5. No config changes required

## v0.0.6 → v0.0.7

Alignment with core v0.1.1: config-driven compaction, cost tracking awareness.

1. Run `/claude-code-hermit:hermit-upgrade` first — core handles `compact.*` config keys and cost tracker improvements
2. In project's CLAUDE.md dev block (`<!-- claude-code-dev-hermit: Development Workflow -->`):
   - Replace the entire block with the updated `state-templates/CLAUDE-APPEND.md`
   - Key change: "Dev Session Hygiene" now splits compaction responsibility — Progress Log compaction is dev-session's job during active work, Monitoring/Session Summary deferred to session-mgr on idle transition
   - New: `## Cost` section noted as auto-populated by core's cost-tracker hook
3. No config changes required — core's `compact.*` keys are in the core config, not dev-hermit's

## v0.0.5 → v0.0.6

Alignment with core v0.0.6–v0.0.10: env system redesign, routines, idle_behavior, YAML frontmatter.

1. Run `/claude-code-hermit:hermit-upgrade` first — it handles core's config migration (env key, routines, idle_behavior, YAML frontmatter on reports/proposals)
2. In project's CLAUDE.md dev block (`<!-- claude-code-dev-hermit: Development Workflow -->`):
   - Replace the entire block with the updated `state-templates/CLAUDE-APPEND.md`
   - Key change: "Idle Agency Awareness" now references `idle_behavior` config key (`"wait"`/`"discover"`) and `## When Idle` OPERATOR.md section
3. Optionally add `## When Idle` to OPERATOR.md with dev-specific maintenance tasks (e.g., "run npm audit", "check stale PRs") — picked up when `idle_behavior` is `"discover"`

No manual change needed for AGENT_HOOK_PROFILE — if you already have strict profile set, the `dev-hatch` skill will find it in `config.json`'s `env` key where core v0.0.7 placed it.

## v0.0.4 → v0.0.5

Alignment with core v0.0.5: `pattern-detect` → `reflect` rename, Docker-first always-on, takeover/hand-back.

1. Run `/claude-code-hermit:hermit-upgrade` to update core's CLAUDE-APPEND block first (adds Docker setup, takeover/hand-back skills, conversational auto-triggers)
2. In project's CLAUDE.md dev block (`<!-- claude-code-dev-hermit: Development Workflow -->`):
   - Replace the entire block with the updated `state-templates/CLAUDE-APPEND.md`
   - Key changes: all `pattern-detect` references → `reflect`
3. In project's CLAUDE.md core contracts section:
   - Contract 7: `pattern-detect` → `reflect`

No config changes required. Core's new Docker/takeover skills are core-only — no dev-hermit wrapping needed.

## v0.0.3 → v0.0.4

Alignment with core v0.0.4: memory-driven learning, idle agency, reflection-based pattern detection.

1. Run `/claude-code-hermit:hermit-upgrade` to update core's CLAUDE-APPEND block first (adds Self-Awareness, Idle Behavior, Daily Rhythm, Learning Model sections)
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

1. Run `/claude-code-hermit:hermit-upgrade` to update core's CLAUDE-APPEND block first
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
