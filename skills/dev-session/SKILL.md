---
name: dev-session
description: Start or resume a development session with full context loading, integrated quality workflow, and structured SHELL.md updates.
---
# /dev-session

Start or resume a development session with full context loading and integrated quality workflow.

## External Dependencies (Optional)

These skills enhance the workflow but are not required:

- `/simplify` — Code cleanup pass. If unavailable, skip this step.
- `/pr-review-toolkit:review-pr` — 6-agent code review. If unavailable, use `reviewer` agent.
- `/batch` — Used by `/dev-parallel` for pattern-based work. If unavailable, fall back to sequential execution.

## Workflow

1. Invoke the `/claude-code-hermit:session-start` skill to check session state and load context.
   Core session-start automatically handles NEXT-TASK.md (offering prepared tasks from accepted proposals) and notifies about unreviewed auto-detected proposals. Dev-session does NOT duplicate these — they happen inside session-start.
2. If resuming an active session:
   - Show: task, progress (completed/remaining plan items), and blockers
   - If the previous session was **blocked**: suggest running `/debug` to diagnose tool/hook failures before re-attempting the blocked work. If `/debug` is not available, skip this suggestion.
   - Ask: "Continue this task, or start a new one?"
3. If starting a new session:
   - If session-start accepted a NEXT-TASK.md, the task is already established — proceed directly to task planning
   - Otherwise, ask: "What's the task for this session?"

### First-session orientation

After session-start completes:

1. Check: is this the first session? (no `S-*-REPORT.md` files in `.claude-code-hermit/sessions/`)
2. If yes:
   - Invoke `repo-mapper` agent with instruction: "Map this codebase"
   - Append repo-mapper's structured output to SHELL.md under a new `## Codebase Orientation` section (insert before `## Plan`)
   - Log in Progress Log: "[HH:MM] First session — ran repo-mapper for codebase orientation"
3. If no: skip — the agent has prior session reports for context
4. If repo-mapper **fails** (timeout, crash, or empty output):
   - Log in Progress Log: "[HH:MM] repo-mapper failed — skipping orientation (non-blocking)"
   - Do NOT retry or block the session
   - Proceed to the next step — the agent can explore manually

### Task planning

After establishing the task:

1. Propose an ordered list of plan items
2. Confirm with the operator before starting work
3. Work through plan items, updating `.claude-code-hermit/sessions/SHELL.md` after each significant plan item

### After implementer returns

Parse the implementer's structured output and update SHELL.md:

1. **Progress Log:** Append `[HH:MM] Implementer completed — branch: <branch name from output>`
2. **Changed section:** Append file list from the "Files Modified" section of implementer output. Format: `- \`path/to/file\` (modified)`
3. **Plan table:** Update the current plan item status to `done` (or `blocked` if implementer reported failures)
4. **Blockers:** If "Test Results" section shows failures or "Concerns" section lists tradeoffs, summarize in Blockers section
5. **Branch tracking:** If no `## Branch` section exists in SHELL.md, add one after `## Session Info` with the branch name

Do NOT silently discard the implementer's structured output. Every section of the output maps to a section of SHELL.md.

> After updating SHELL.md, check line count. If over 150 lines, compact the Progress Log
> (summarize older entries into a 5-10 line block, keep last 10 in detail).
> Core's session hygiene rules apply — see core's CLAUDE-APPEND.md.

## Post-Implementation Quality Pass

After implementation steps complete (via `implementer` agent or direct edits):

### /simplify duplicate check

Before running /simplify at the session level:

- Check SHELL.md progress log for evidence that /batch was used (look for "batch" or "parallel" in plan item completion notes)
- If /batch was used: skip /simplify — batch already ran it on each worker's output. Log: "Skipping /simplify — already applied by /batch"
- If implementer agent was used (single task): /simplify has NOT run (subagents can't invoke skills) — proceed with /simplify
- If edits were made directly in the main session: proceed with /simplify

### Quality steps

1. **Run tests** — confirm the implementation works
2. Check: is `/simplify` available?
   - If NOT available: skip to step 6 (proceed to review)
   - If available and not already applied by /batch: continue to step 3
3. **Run `/simplify`** on changed files
4. **Run tests again** — confirm `/simplify` didn't break anything
5. If tests fail after `/simplify`:
   - Revert the /simplify changes (git checkout on affected files)
   - Note in SHELL.md: "simplify caused test regression — committed without simplification"
   - Proceed to review with the pre-simplify code
6. **Run `/pr-review-toolkit:review-pr`** for a comprehensive code review
   - This invokes 6 specialist review agents (code quality, tests, comments, error handling, type design, simplification) directly in the main session
   - The `reviewer` agent is available as a **fallback** if `/review` fails, produces no output, or the operator explicitly requests it (e.g., for a quick read-only scan or non-PR work)
   - If critical issues are found: loop back to implementation to address them
   - If clean or minor issues only: proceed

## Task Complete

When the task is complete (or the operator decides to move on, even if partial or blocked):

### Idle transition

1. Finalize SHELL.md — ensure all progress, blockers, and findings are recorded
2. Verify the Dev Task Completion Checklist (defined in CLAUDE-APPEND.md)
3. If task completing as `blocked`: suggest `/debug` to check tool/hook failures before archiving. If `/debug` is not available, skip this suggestion. If the operator accepts and it unblocks the task, resume work instead of archiving.
4. Create proposals for any high-leverage improvements discovered during work (use dev proposal categories: [missing-tests], [tech-debt], [dependency], [tooling], [architecture])
5. Invoke the `reflect` skill to reflect on the completed task (uses memory, SHELL.md, and cost log). Reflect also fires independently via heartbeat, natural pauses, and end of day — invoking it here ensures task boundaries are always a reflection point.
6. Use `session-mgr` to perform an **idle transition** (archive report as S-NNN, reset task-scoped sections, set status to idle)
7. If `heartbeat.enabled` is true in config and heartbeat is not already running: start it (`/claude-code-hermit:heartbeat start`)
8. Report: "Task archived as S-NNN. What's next?"
9. Once the operator provides a new task: go back to the **Task planning** step

To end the session entirely, the operator runs `/claude-code-hermit:session-close` at any time.
