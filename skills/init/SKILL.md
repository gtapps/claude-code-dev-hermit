---
name: init
description: Initializes the dev pack in the current project. Requires claude-code-hermit core to be initialized first. Appends dev-specific CLAUDE.md block, recommends strict profile, and offers dev heartbeat items.
---
# Initialize Dev Pack

Set up the development workflow pack for this project. Requires claude-code-hermit core.

## Steps

### 1. Check prerequisites

Check if `.claude/.claude-code-hermit/` exists in the current project.
- If it does NOT exist: tell the operator to run `/claude-code-hermit:init` first, then stop.
- If it exists: read `.claude/.claude-code-hermit/config.json` and check that `_hermit_versions["claude-code-hermit"]` is present. If missing: warn the operator that this dev pack requires claude-code-hermit and suggest running `/claude-code-hermit:init` first. Allow proceeding if the operator chooses to continue anyway.
- If it exists and version is present: proceed.

### 2. Check if already initialized

Check if `CLAUDE.md` contains `<!-- claude-code-dev-hermit: Development Workflow -->`.
- If yes: inform the operator the dev pack is already initialized. Ask if they want to reinitialize (to pick up new version content).
  - If yes: remove the existing dev pack block — starting from the `---` line immediately before `<!-- claude-code-dev-hermit: Development Workflow -->`, through to the next `---` on its own line (outside code fences) or EOF. Then proceed to step 3.
  - If no: skip step 3 (proceed directly to step 4 — profile and heartbeat checks still run).
- If no: proceed.

### 3. Append dev workflow to CLAUDE.md

Read `${CLAUDE_PLUGIN_ROOT}/state-templates/CLAUDE-APPEND.md` and append its contents to the end of CLAUDE.md.

### 4. Recommend strict profile

Check `.claude/settings.json` for `env.AGENT_HOOK_PROFILE`.

- If set to `strict`: good, inform that git-push-guard is active
- If set to `standard` or not set:
  - Inform: "The dev pack includes a git-push-guard hook that blocks direct pushes to main and --no-verify usage. This hook only activates at the `strict` profile."
  - Ask: "Enable strict profile? (yes / no) [yes]"
  - If yes: update `.claude/settings.json` to set `AGENT_HOOK_PROFILE=strict`
  - If no: note that git safety hooks are inactive

### 5. Suggest dev heartbeat items

Check if `.claude/.claude-code-hermit/HEARTBEAT.md` exists:

- If it exists:
  - Read current contents
  - Check if dev-specific items are already present (look for "test suite" or "feature branch" markers)
  - If not present, offer to append:
    ```
    # Dev Checks (added by claude-code-dev-hermit)
    - Check if the test suite is passing on the current branch
    - Check for uncommitted changes on active feature branches
    - Check for stale feature branches (>7 days without commits)
    - Quick scan: any dependency security advisories? (npm audit / pip audit)
    ```
  - Note: Core includes heartbeat self-evaluation. After every 20 ticks (configurable via `heartbeat.self_eval_interval`), the heartbeat suggests removing stale items and adding relevant ones based on auto-detected proposals. The dev items above are starting suggestions that self-evaluation may refine over time.
  - If operator declines: skip
- If it doesn't exist: inform operator that heartbeat is a core feature and suggest running `/claude-code-hermit:heartbeat edit` to set it up

### 6. Report results

Check if OPERATOR.md exists and contains `## Development Conventions` to determine dev conventions status. If OPERATOR.md does not exist, treat as "not yet captured."

Print a summary (adapt lines to reflect what actually happened):

```
Dev pack initialized!

Updated:
  CLAUDE.md — dev workflow block [appended / replaced / already present, unchanged]
  Hook profile: [strict (git-push-guard active) / standard (git safety hooks inactive)]
  Dev heartbeat items: [added / skipped / heartbeat not configured]
  Dev conventions: [captured in OPERATOR.md / not yet in OPERATOR.md — re-run /claude-code-hermit:init to capture, or add manually]

Available skills:
  /claude-code-dev-hermit:dev-session   — orchestrated development session
  /claude-code-dev-hermit:dev-parallel  — parallel work strategies
  /claude-code-dev-hermit:dev-cleanup   — branch cleanup

Available agents:
  repo-mapper   — codebase orientation (Haiku)
  implementer   — code writing in worktree (Sonnet)
  reviewer      — code review, read-only (Sonnet)

Core learning loop (from claude-code-hermit):
  /claude-code-hermit:proposal-list   — view proposals (including auto-detected)
  /claude-code-hermit:proposal-act    — accept, defer, or dismiss proposals
  /claude-code-hermit:pattern-detect  — runs automatically at session close
```
