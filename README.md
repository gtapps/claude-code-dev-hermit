# claude-code-dev-hermit

A development workflow plugin for [claude-code-hermit](https://github.com/gtapps/claude-code-hermit) — git safety, quality gates, and a code-writing agent that works on branches so you don't have to worry about main.

Think of it as the dev muscle for your hermit. Core handles sessions and learning. This handles building things.

## Requires

- [claude-code-hermit](https://github.com/gtapps/claude-code-hermit) v0.2.4+ (installed and hatched)

## Install

```bash
# Install core first (skip if already done)
claude plugin marketplace add gtapps/claude-code-hermit
claude plugin install claude-code-hermit@claude-code-hermit --scope project
/claude-code-hermit:hatch

# Then install the dev plugin
claude plugin marketplace add gtapps/claude-code-dev-hermit
claude plugin install claude-code-dev-hermit@claude-code-dev-hermit --scope project
/claude-code-dev-hermit:dev-hatch
```

The setup wizard asks about your branch naming, deploy process, and hook profile. It also offers companion plugins (code-review, feature-dev, context7) and suggests dev-specific heartbeat checks.

## How It Works

Your hermit already knows how to manage sessions, track costs, and learn from its work. This plugin adds a development workflow on top:

1. **Plan** — break the task into steps (native Tasks)
2. **Implement** — delegate to the `implementer` agent, which works in an isolated git worktree on a feature branch
3. **Quality pass** — run `/claude-code-dev-hermit:dev-quality` (tests, `/simplify`, tests again, code review)
4. **Reflect** — the hermit reflects on what it learned before moving on

The implementer never touches main. It commits to feature branches, runs tests, and hands back a structured summary. You review and merge on your terms.

## What's Included

### Agent

| Agent | Model | What it does |
|-------|-------|-------------|
| `implementer` | Sonnet | Writes code in an isolated worktree. Feature branches only. Tests before and after. |

### Skills

| Skill | What it does |
|-------|-------------|
| `dev-hatch` | One-time project setup — appends dev workflow to CLAUDE.md, configures git safety, installs companion plugins |
| `dev-quality` | Post-implementation quality pass — tests, simplify, tests, code review |
| `dev-cleanup` | Lists stale/merged branches and offers to clean them up safely |

### Git Safety

The `git-push-guard` hook blocks dangerous git operations:

- Direct push to main/master
- `--no-verify` on any command
- Force push on any branch
- `--force-with-lease` to main/master

The hook only activates at **strict** profile. At standard (default), git commands are unchecked by hooks. The implementer agent also has its own prompt-level rules that always apply — no push, no `--no-verify`, no commits to main.

| Profile | Hook enforcement |
|---------|-----------------|
| `minimal` | None |
| `standard` (default) | None |
| `strict` | git-push-guard active |

## Built-in Skills Used

These are Claude Code built-ins — no installation needed:

- `/simplify` — code cleanup after implementation
- `/batch` — same change across many files in parallel
- `/debug` — diagnostics when something's stuck

## Documentation

- [How to Use](docs/HOW-TO-USE.md) — day-to-day workflow and tips
- [Skills Reference](docs/SKILLS.md) — detailed skill documentation
- [Git Safety](docs/GIT-SAFETY.md) — hook details and profile configuration
- [Recommended Plugins](docs/RECOMMENDED-PLUGINS.md) — companion plugins offered during setup

## License

MIT
