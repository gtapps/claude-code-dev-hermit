# Dev Pack Questions for OPERATOR.md Onboarding
# Read by claude-code-hermit:init step 5a (domain pack extension)
# These questions are appended to core's onboarding batch.

| # | Question | Skip if... | Maps to section |
|---|----------|-----------|----------------|
| 1 | What branch naming convention do you use? (e.g., `feature/*`, `fix/*`, or freeform) | Git history shows a clear pattern (check `git log --oneline -20` for branch merge patterns) | ## Development Conventions |
| 2 | Which branches are protected from direct push? (e.g., main, staging, production) | Only one branch exists (solo project with no branching) | ## Development Conventions |
| 3 | What's the deploy process? (manual, CI auto-deploy, staging first, etc.) | CLAUDE.md or CI config already describes the deploy pipeline | ## Development Conventions |
| 4 | Code review expectations? (PRs required, self-review ok, specific reviewers) | User already identified as solo developer in core's team size question | ## Development Conventions |
