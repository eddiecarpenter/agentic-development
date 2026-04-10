# Dev Session — Stage 4

## ⛔ Automation-Only — Do Not Execute Interactively

This session is triggered exclusively by GitHub Actions when a Feature issue receives
the `in-development` label. It must never be run manually by an agent in an interactive session.

If you are reading this skill in an interactive session, stop immediately and print:

```
REFUSED: Dev Session is automation-only.
It runs automatically when in-development is applied.
Do not execute this session interactively.
```

Do not proceed past this point in an interactive context.

---

## Purpose

Implement all open Task sub-issues on the feature branch, in order.

## When it Runs

Triggered automatically by GitHub Actions when a Feature issue is labelled `in-development`.

## What the Agent Does

1. Verifies it is on the correct feature branch — never works on main
2. Reads the Feature issue and extracts acceptance criteria for end-of-session verification
3. Queries open Task sub-issues on the Feature, ordered by issue number
4. If `recovery.md` does not exist at session start, proceed as a fresh start — no
   recovery behaviour occurs
5. For each Task in order:
   - Reads the task issue and understands what must be built
   - Implements the work described
   - Builds and tests — stops immediately on failure and reports the exact error
   - Commits: `feat: [task description] — task N of N (#feature-issue)`
   - Closes the task issue
   - **Writes `recovery.md`** — after the commit and close, write `recovery.md` to
     the repo root with the current progress state (see format below), then:
     ```bash
     git add recovery.md
     git commit -m "chore: update recovery.md — task N of N (#feature-issue)"
     git push
     ```
     This must happen *after* each successful task commit and *before* starting the
     next task. It ensures that if the session dies, the next session can resume from
     the recorded state.
6. Verifies each acceptance criterion has test coverage — stops if any criterion is uncovered
7. When all tasks are closed and criteria verified — prints: `=== Dev Session — Completed ===`
8. Exits cleanly — the workflow pushes and opens the PR automatically

## recovery.md Format

After each task commit, write `recovery.md` to the repo root with exactly this structure:

```markdown
# Recovery State

| Field               | Value                              |
|---------------------|------------------------------------|
| Feature issue       | #<feature-issue-number>            |
| Branch              | <current-branch-name>              |
| Total tasks         | <total-task-count>                 |
| Last updated        | <ISO 8601 timestamp>               |

## Completed Tasks

- [x] #<issue-number> — <task-title>
- [x] #<issue-number> — <task-title>

## Remaining Tasks

- [ ] #<issue-number> — <task-title> ← current
- [ ] #<issue-number> — <task-title>
```

**Field definitions:**
- **Feature issue** — the parent Feature issue number (e.g. `#197`)
- **Branch** — the current branch name from `git branch --show-current`
- **Total tasks** — the total number of Task sub-issues at session start
- **Last updated** — ISO 8601 timestamp (`date -u +%Y-%m-%dT%H:%M:%SZ`)
- **Completed Tasks** — each task committed and closed so far, in order
- **Remaining Tasks** — each task not yet completed; mark the next task with `← current`

## Rules

- Never commit on main
- Never skip a failing test — fix it before moving to the next task
- Never claim a task complete without running build and tests
- A feature is not complete until all acceptance criteria have test coverage
- Report exact command output on any failure
- Follow the standards in `.ai/standards/<stack>.md` exactly
- **Inline status updates**: this skill does not apply pipeline labels (the workflow
  applies `in-review`). If a future change adds a pipeline label transition here, it
  must include an inline project status update following `set-issue-status.md` —
  hard-fail if `AGENTIC_PROJECT_ID` is not set

## Notification

Before exiting, notify the user: "PR #N is ready for your review."

## Next Step

The workflow pushes the branch and opens a PR with `Closes #N`.
Human review happens in the PR. If review comments need addressing, the
**PR Review Session (Stage 4b)** recipe handles that.
