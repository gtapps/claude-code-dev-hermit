---
name: dev-session
description: Start or resume a development session with full context loading, integrated quality workflow, and structured ACTIVE.md updates.
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
   Core session-start automatically handles NEXT-MISSION.md (offering prepared missions from accepted proposals) and notifies about unreviewed auto-detected proposals. Dev-session does NOT duplicate these — they happen inside session-start.
2. If resuming an active session:
   - Show: mission, progress (completed/remaining steps), and blockers
   - If the previous session was **blocked**: suggest running `/debug` to diagnose tool/hook failures before re-attempting the blocked work. If `/debug` is not available, skip this suggestion.
   - Ask: "Continue this mission, or start a new one?"
3. If starting a new session:
   - If session-start accepted a NEXT-MISSION.md, the mission is already established — proceed directly to mission planning
   - Otherwise, ask: "What's the mission for this session?"

### First-session orientation

After session-start completes:

1. Check: is this the first session? (no `S-*-REPORT.md` files in `.claude/.claude-code-hermit/sessions/`)
2. If yes:
   - Invoke `repo-mapper` agent with instruction: "Map this codebase"
   - Append repo-mapper's structured output to ACTIVE.md under a new `## Codebase Orientation` section (insert before `## Steps`)
   - Log in Progress Log: "[HH:MM] First session — ran repo-mapper for codebase orientation"
3. If no: skip — the agent has prior session reports for context
4. If repo-mapper **fails** (timeout, crash, or empty output):
   - Log in Progress Log: "[HH:MM] repo-mapper failed — skipping orientation (non-blocking)"
   - Do NOT retry or block the session
   - Proceed to the next step — the agent can explore manually

### Mission planning

After establishing the mission:

1. Propose an ordered list of steps
2. Confirm with the operator before starting work
3. Work through steps, updating `.claude/.claude-code-hermit/sessions/ACTIVE.md` after each significant step

### After implementer returns

Parse the implementer's structured output and update ACTIVE.md:

1. **Progress Log:** Append `[HH:MM] Implementer completed — branch: <branch name from output>`
2. **Changed section:** Append file list from the "Files Modified" section of implementer output. Format: `- \`path/to/file\` (modified)`
3. **Steps table:** Update the current step status to `done` (or `blocked` if implementer reported failures)
4. **Blockers:** If "Test Results" section shows failures or "Concerns" section lists tradeoffs, summarize in Blockers section
5. **Branch tracking:** If no `## Branch` section exists in ACTIVE.md, add one after `## Session Info` with the branch name

Do NOT silently discard the implementer's structured output. Every section of the output maps to a section of ACTIVE.md.

> After updating ACTIVE.md, check line count. If over 150 lines, compact the Progress Log
> (summarize older entries into a 5-10 line block, keep last 10 in detail).
> Core's session hygiene rules apply — see core's CLAUDE-APPEND.md.

## Post-Implementation Quality Pass

After implementation steps complete (via `implementer` agent or direct edits):

### /simplify duplicate check

Before running /simplify at the session level:

- Check ACTIVE.md progress log for evidence that /batch was used (look for "batch" or "parallel" in step completion notes)
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
   - Note in ACTIVE.md: "simplify caused test regression — committed without simplification"
   - Proceed to review with the pre-simplify code
6. **Run `/pr-review-toolkit:review-pr`** for a comprehensive code review
   - This invokes 6 specialist review agents (code quality, tests, comments, error handling, type design, simplification) directly in the main session
   - The `reviewer` agent is available as a **fallback** if `/review` fails, produces no output, or the operator explicitly requests it (e.g., for a quick read-only scan or non-PR work)
   - If critical issues are found: loop back to implementation to address them
   - If clean or minor issues only: proceed

## Session Close

When the operator signals they're done (or you complete the mission):

- If the session is closing with status `blocked`: suggest running `/debug` to check for tool or hook failures before closing. If `/debug` is not available, skip this suggestion.
- Invoke the `/claude-code-hermit:session-close` skill.
  - Core's session-close determines the close mode based on `always_on` config: **idle transition** (mission archived, session stays open for next mission) or **full shutdown** (session archived, ACTIVE.md reset). The dev close checklist below applies to both modes unless noted.
  - Core session-close automatically invokes `pattern-detect` before archiving. Do NOT invoke pattern-detect separately.
  - Pattern-detect analyzes recent session reports for recurring blockers, workarounds, cost trends, and tag correlations, auto-creating proposals when patterns appear 3+ times.
  - Dev-specific proposal categories ([missing-tests], [tech-debt], [dependency], [tooling], [architecture]) are for manual proposals created during dev sessions. Auto-detected proposals use core categories ([blocker], [workaround], [cost-trend], [tag-correlation]).

### Dev close checklist (both modes unless noted)

- Feature branch is committed (no uncommitted changes in worktree)
- Tests pass on the feature branch
- Branch name is recorded in ACTIVE.md
- If implementation is partial: "Next Start Point" describes what remains **(full shutdown only)** — idle transitions use Session Summary instead
- If reviewer was invoked: recommendation recorded in Progress Log
- If /simplify was run: note whether applied or reverted

## Dev-Specific Pattern Categories

Use the dev proposal categories defined in CLAUDE.md (under "Dev Proposal Categories") when creating manual proposals. Core auto-detection categories ([blocker], [workaround], [cost-trend], [tag-correlation]) are assigned automatically by pattern-detect at session close.
