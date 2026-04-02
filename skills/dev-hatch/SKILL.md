---
name: dev-hatch
description: Activates the dev hermit in the current project. Appends dev workflow to CLAUDE.md, configures git safety, suggests dev heartbeat items, and installs companion plugins. Run once per project after /claude-code-hermit:hatch.
---
# Activate Dev Hermit

Set up development workflow for this project. Requires claude-code-hermit core to be initialized first.

## Plan

### 1. Check prerequisites

Check if `.claude-code-hermit/` exists in the current project.
- If it does NOT exist: tell the operator to run `/claude-code-hermit:hatch` first, then stop.
- If it exists: read `.claude-code-hermit/config.json` and check that `_hermit_versions["claude-code-hermit"]` is present. If missing: warn and suggest running `/claude-code-hermit:hatch` first. Allow proceeding if the operator chooses.
- If version is present but less than `0.2.4`: warn that core v0.2.4+ is required and recommend `/claude-code-hermit:hermit-upgrade`. Allow proceeding.
- If version is `0.2.4` or later: proceed.
- Check `.claude/settings.local.json` for `CLAUDE_CODE_TASK_LIST_ID`. If missing, warn: "Task tracking requires core v0.2.4+ init. Run `/claude-code-hermit:hatch` to set it up."

### 2. Check if already activated

Check if `CLAUDE.md` contains `<!-- claude-code-dev-hermit: Development Workflow -->`.
- If yes: inform the operator the dev hermit is already activated. Ask if they want to reinitialize (to pick up new version content).
  - If yes: remove the existing dev hermit block — starting from the `---` line immediately before `<!-- claude-code-dev-hermit: Development Workflow -->`, through to the next `---` on its own line (outside code fences) or EOF. Then proceed to step 3.
  - If no: skip step 3 (proceed directly to step 4).
- If no: proceed.

### 3. Append dev workflow to CLAUDE.md

Read `${CLAUDE_PLUGIN_ROOT}/state-templates/CLAUDE-APPEND.md` and append its contents to the end of CLAUDE.md.

### 4. Setup wizard

Collect dev preferences through a focused wizard using `AskUserQuestion`.

#### Phase 1 — Auto-detect (silent, parallel)

Run these Bash commands in a single turn before asking anything:
- Branch naming: `git log --oneline --all -20 | grep -oE '(feature|fix|hotfix|chore|release)/[^ ]+' | head -5`
- Protected branches: `git branch -r 2>/dev/null | grep -E 'main|master|staging|production' | head -5`
- CI config: `ls .github/workflows/ 2>/dev/null; ls .gitlab-ci.yml 2>/dev/null; ls Jenkinsfile 2>/dev/null`
- Test commands: `grep -E '"test"' package.json 2>/dev/null; ls pytest.ini setup.cfg pyproject.toml 2>/dev/null | head -3`
- Current profile: read `env.AGENT_HOOK_PROFILE` from config.json (already loaded in step 1)

Collect findings silently.

#### Phase 2 — Git safety and conventions (AskUserQuestion batch)

Only ask questions whose answers could NOT be auto-detected. For auto-detected values, confirm: "Detected: feature/* branch naming from git history."

```
questions: [
  {
    header: "Hook profile",
    question: "Enable git-push-guard? Blocks direct pushes to main, --no-verify, and force push.",
    options: [
      { label: "Strict — git-push-guard active (recommended)" },
      { label: "Standard — no hook enforcement" }
    ]
  },
  {
    header: "Branch naming",
    question: "Branch naming convention? [auto-detected: {pattern or 'none detected'}]"
  },
  {
    header: "Deploy process",
    question: "Deploy process? (manual, CI auto-deploy, staging first, etc.) [skip if unknown]"
  },
  {
    header: "Code review",
    question: "Code review expectations? (PRs required, self-review ok) [skip if solo]"
  }
]
```

Skip the hook profile question if already set to `strict` — just confirm: "git-push-guard is already active (strict profile)."

Record profile choice. If `strict`: update `env.AGENT_HOOK_PROFILE` in config.json.

If OPERATOR.md exists but does NOT contain `## Development Conventions`, append answers under that heading. If the section already exists, skip writing.

#### Phase 3 — Companion plugins (AskUserQuestion)

Check which plugins are already installed (`claude plugin list` or `.claude/settings.json`). Only offer plugins NOT already present. If all are installed, skip this phase.

```
questions: [
  {
    header: "Companion plugins",
    question: "Install recommended dev plugins?",
    options: [
      { label: "All — code-review + feature-dev + context7 (recommended)" },
      { label: "Pick — choose individually" },
      { label: "Skip — install nothing" }
    ]
  }
]
```

Plugins:
- `code-review@claude-plugins-official` — official code review (replaces deprecated built-in /review)
- `feature-dev@claude-plugins-official` — guided feature development with codebase understanding
- `context7@claude-plugins-official` — live documentation lookup for framework APIs

If **pick**: ask about each with a separate `AskUserQuestion` (yes/no per plugin).

For each accepted plugin:
1. Run `claude plugin install <plugin>@claude-plugins-official --scope project`
2. Add to `docker.recommended_plugins` in `.claude-code-hermit/config.json`

### 5. Suggest dev heartbeat items

Check if `.claude-code-hermit/HEARTBEAT.md` exists:

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
  - If operator declines: skip
- If it doesn't exist: inform operator that heartbeat is a core feature and suggest running `/claude-code-hermit:heartbeat edit` to set it up

### 6. Stamp version

Read `${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json` to get the current plugin version. Write it into `.claude-code-hermit/config.json` under `_hermit_versions["claude-code-dev-hermit"]`.

### 7. Report results

Print a summary (adapt lines to reflect what actually happened):

```
Dev hermit activated!

Git safety:
  Hook profile: [strict (git-push-guard active) / standard (no enforcement)]
  Branch naming: [feature/* / detected from history / not configured]

Updated:
  CLAUDE.md — dev workflow block [appended / replaced / already present]
  OPERATOR.md — dev conventions [added / already present / skipped]
  Dev heartbeat items: [added / skipped / heartbeat not configured]
  config.json — dev hermit version stamped

Companion plugins:
  [code-review + feature-dev + context7 / partial / skipped]

Available skills:
  /claude-code-dev-hermit:dev-quality   — post-implementation quality pass
  /claude-code-dev-hermit:dev-cleanup   — branch cleanup

Available agent:
  implementer   — code writing in worktree (Sonnet)
```
