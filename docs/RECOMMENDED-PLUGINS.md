# Recommended Plugins

The `dev-hatch` wizard offers these during setup. All are official plugins from `claude-plugins-official` — install individually or accept them all at once.

---

## code-review

```bash
claude plugin install code-review@claude-plugins-official --scope project
```

Official Anthropic plugin - Code review for PRs and changed files. The `dev-quality` skill uses this as its final step — after tests pass and `/simplify` runs, code-review checks for bugs, security issues, and convention violations.

Without this plugin, the quality pass skips the review step.

---

## feature-dev

```bash
claude plugin install feature-dev@claude-plugins-official --scope project
```

Official Anthropic plugin - Guided feature development with codebase understanding. Useful when you want a structured approach to building a new feature — it analyzes the existing codebase, designs an architecture, and walks through implementation.

Works independently from the dev hermit workflow. Use it when you want more guidance on _what_ to build before the hermit builds it.

---

## context7

```bash
claude plugin install context7@claude-plugins-official --scope project
```

Live documentation lookup for framework APIs. Instead of relying on training data (which may be outdated), context7 fetches current docs for libraries like React, Next.js, Django, Express, etc.

Useful when the implementer agent or you are working with frameworks where API details change between versions.

---

## Docker Auto-Install

When you accept plugins during `dev-hatch`, they're added to `docker.recommended_plugins` in `.claude-code-hermit/config.json`. The `/docker-setup` skill reads this list and includes them in the generated Docker configuration — so your always-on hermit gets the same plugins without manual setup.
