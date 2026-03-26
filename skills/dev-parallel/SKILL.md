---
name: dev-parallel
description: Launch parallel work for the current session. Routes to /batch (pattern-based) or Agent Teams (independent tasks) with fallback to sequential execution.
---
# /dev-parallel

Launch parallel work for the current session task. Routes to the best parallel execution strategy based on the nature of the work.

## Prerequisites
- An active session with a defined task (`.claude/.claude-code-hermit/sessions/SHELL.md` must exist)

## External Dependencies (Optional)

- `/batch` — Parallel pattern-based execution. If unavailable, falls back to sequential `implementer` agent execution.
- `/simplify` — Code cleanup (run in main session after parallel work completes). If unavailable, skip.
- `/pr-review-toolkit:review-pr` — Code review. If unavailable, use `reviewer` agent.

## Parallel Strategy Routing

### 1. Assess the parallel work

- Read `.claude/.claude-code-hermit/sessions/SHELL.md` for the current task and plan
- Identify plan items that have no dependencies on each other
- Classify the work:

### 2. Pattern-based work (apply X to every file matching Y)

Same type of change across many files (e.g., "migrate all components from X to Y", "add error handling to all API routes"):

1. Route to `/batch` (if available)
   - Update SHELL.md: mark parallel items as `in_progress`, note start time
   - Draft a well-formed `/batch` instruction from SHELL.md task descriptions
   - Present the draft to the operator for confirmation
   - The operator runs `/batch` with the confirmed instruction
   - `/batch` handles: planning, decomposition (5-30 units), worktree isolation, parallel execution, and auto-runs `/simplify` on each worker's output
   - **After `/batch` completes:** Update SHELL.md with results — branches created, PRs opened, which items succeeded/failed

2. If `/batch` not available: fall back to sequential execution of each item using the `implementer` agent

**Limitations to state clearly:**
- `/batch` workers build context from the codebase and CLAUDE.md (loaded automatically), but do NOT have access to OPERATOR.md constraints
- OPERATOR.md constraints are enforced via the post-batch review step (the main session runs `/review` after `/batch` completes)

### 3. Independent different tasks (task A: add auth, task B: fix schema)

Distinct changes in different areas:

1. Check if `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` is set
2. If set: use Agent Teams with implementer constraints per teammate
   - Update SHELL.md: mark parallel items as `in_progress`, note start time
   - For each independent task, spawn a teammate using the Agent tool:
     - Clear, bounded task description
     - `implementer` agent constraints (worktree isolation, no push, no --no-verify)
     - Its own branch (e.g., `feature/task-1-description`)
   - Monitor progress, collect results
   - **After all teammates finish:** Run `/simplify` on each branch's changes in the main session (teammates cannot invoke skills). Then run `/pr-review-toolkit:review-pr` for review.
   - Update SHELL.md with consolidated results
3. If NOT set: fall back to sequential execution via `implementer` agent, one task at a time
4. Do NOT suggest `/batch` as an alternative — `/batch` is for uniform patterns, not independent tasks

### 4. Ambiguous (unclear if pattern or independent)

- Ask the operator which strategy to use

## Present Results

- Show what each unit/teammate accomplished
- Include review findings
- Ask how to proceed: merge, revise, or discard each branch

After parallel results are accepted and merged, the quality pass runs as normal, followed by dev-session's task-complete flow — the task is archived and the agent waits for the next task. Do not duplicate the idle transition logic here.

## Heuristic

> **Same change across N files → `/batch`.** Different changes on different areas → **Agent Teams.**
>
> When in doubt, ask the operator which approach they prefer.

## Pattern Tracking Note

Parallel work sessions may generate patterns worth tracking (e.g., the same blocker appearing across multiple parallel workers). These patterns will be picked up by `pattern-detect` at the next task boundary (idle transition) — no special handling is needed during parallel work. However, if you notice a recurring issue across workers, consider creating a manual proposal with an appropriate dev category prefix (e.g., [tooling], [architecture]) via `/claude-code-hermit:proposal-create`.
