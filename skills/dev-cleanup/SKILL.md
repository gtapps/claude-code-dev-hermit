---
name: dev-cleanup
description: Lists and offers to clean up stale or merged git branches. Use when the local repo has accumulated feature branches across sessions.
---
# Dev Cleanup

List local branches and identify cleanup candidates.

## Prerequisites

- Verify `.claude-code-hermit/sessions/` exists (core + dev hermit initialized)
- If not: inform the operator to run `/claude-code-hermit:init` and `/claude-code-dev-hermit:init` first, then exit

## Plan

1. Run `git branch --merged main` to find fully merged branches
2. Run `git branch -v` to find branches with no recent commits
3. Cross-reference with `.claude-code-hermit/sessions/SHELL.md`
   **and** all `S-*-REPORT.md` files in the sessions directory to
   identify the current working branch and recently active branches
   (do NOT suggest deleting them).
   Also check `.claude-code-hermit/sessions/NEXT-TASK.md` — if it
   exists and references a branch name, do NOT suggest deleting that branch
   (it is part of a pending task from an accepted proposal)
4. Present a table:

   | Branch          | Status             | Last Commit | Suggestion             |
   | --------------- | ------------------ | ----------- | ---------------------- |
   | feature/auth    | merged             | 3 days ago  | safe to delete         |
   | fix/typo        | merged             | 7 days ago  | safe to delete         |
   | feature/api-v2  | active (SHELL.md) | today       | keep                   |
   | experiment/perf | unmerged           | 14 days ago | review before deleting |

5. Ask operator which branches to delete
6. Delete only confirmed branches with `git branch -d` (safe delete)
7. For unmerged branches the operator confirms: use `git branch -D`
   only with explicit confirmation

## Rules

- Never delete the current branch or main/master
- Never force-delete without explicit operator confirmation per branch
- Never delete remote branches — local cleanup only
- Cross-reference branches against ALL session reports (`S-*-REPORT.md`),
  not just SHELL.md — a branch may belong to a paused or prior session
- Never delete branches referenced in NEXT-TASK.md (pending accepted proposal tasks)
- Log deletions in SHELL.md Progress Log
