# claude-code-dev-hermit

Software development agents, workflows, and git safety for [claude-code-hermit](https://github.com/gtapps/claude-code-hermit).

## Requires

- `claude-code-hermit` v0.0.3+ (core)

## Install

```
claude plugin marketplace add gtapps/claude-code-hermit
claude plugin install claude-code-hermit@claude-code-hermit --scope project
/claude-code-hermit:init

claude plugin marketplace add gtapps/claude-code-dev-hermit
claude plugin install claude-code-dev-hermit@claude-code-dev-hermit --scope project
/claude-code-dev-hermit:init
```

## What's Included

- **3 agents:** repo-mapper, implementer, reviewer
- **3 skills:** dev-session, dev-parallel, dev-cleanup
- **1 init skill:** project setup and profile configuration
- **1 hook:** git-push-guard (strict profile only)

## Agents

| Agent | Model | Purpose |
|-------|-------|---------|
| `repo-mapper` | Haiku | Read-only codebase scanning for orientation, file discovery, and dependency mapping |
| `implementer` | Sonnet | Code writing in an isolated worktree — feature implementation, bug fixes, refactoring |
| `reviewer` | Sonnet | Read-only code review for quality, correctness, security, and convention adherence |

## Skills

| Skill | Description |
|-------|-------------|
| `/claude-code-dev-hermit:dev-session` | Orchestrated development session with quality workflow |
| `/claude-code-dev-hermit:dev-parallel` | Parallel work routing (pattern → `/batch`, independent → Agent Teams) |
| `/claude-code-dev-hermit:dev-cleanup` | List and clean up stale or merged git branches |
| `/claude-code-dev-hermit:init` | Initialize the dev hermit in a target project |

## Git Safety

The `git-push-guard` hook blocks:

- Direct `git push` to main/master
- `--no-verify` on any command
- Force push (`--force` or `-f`) on any branch
- `--force-with-lease` to main/master

This hook only activates at the **strict** profile (`AGENT_HOOK_PROFILE=strict`). At `standard` (default), git commands are unchecked by hooks. The `init` skill offers to enable strict profile during setup.

## Hook Profiles

| Profile | Git Safety |
|---------|-----------|
| `minimal` | No hook enforcement |
| `standard` (default) | No hook enforcement |
| `strict` | git-push-guard active — blocks pushes to main, --no-verify, force push |

## Optional Dependencies

These external skills enhance the workflow but are not required:

| Skill | Purpose | Fallback |
|-------|---------|----------|
| `/simplify` | Code cleanup pass | Skipped if unavailable |
| `/pr-review-toolkit:review-pr` | 6-agent comprehensive code review | Falls back to `reviewer` agent |
| `/batch` | Parallel pattern-based execution | Sequential `implementer` execution |

## License

MIT
