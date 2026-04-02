# AGENTS.md — Agent Protocol

This file governs how AI agents behave in this repository and all domain repos.
It is language-agnostic and reusable across projects.

**This file is managed by the `agentic-development` template. Do not edit manually.
Local overrides belong in `AGENTS.local.md`.**

---

## Session Initialisation

At the start of every session, read these sources in order before doing anything else:

1. `docs/PROJECT_BRIEF.md` — what the agentic system is and how it works
2. Read `REPOS.md`. For each repo with status `active`, derive its local directory as
   `<type>s/<name>` (e.g. `type: domain` → `domains/<name>`, `type: tool` → `tools/<name>`).
   For each unique type, ensure the type folder (`<type>s/`) exists — if not:
   a. Create the folder with a `.gitkeep` file
   b. Stage it: `git add <type>s/.gitkeep`
   c. Add `<type>s/*/` to `.gitignore` and stage that too: `git add .gitignore`
   d. Commit both: `chore: bootstrap <type>s/ directory`
   Check whether each `<type>s/<name>` directory exists locally. If any repos are
   missing, list them and ask the user whether to clone them before proceeding.
   Clone command: `git clone <repo> <type>s/<name>`
3. Query open Requirement issues in the agentic repo:
   `gh issue list --repo <agentic-repo> --label requirement --state open --json number,title,labels`
4. For domain sessions — query open Feature issues in the domain repo:
   `gh issue list --label feature --state open --json number,title,labels,body`
5. Read the relevant standards file from `base/standards/` for the domain language
   (e.g. `base/standards/go.md` for Go domains)

Do not skip any step. Do not begin work until all steps are complete.

There is no STATUS.md. Current state is derived from GitHub Issues.

---

## Session Types

### Requirements Session (Phase 1)

Run interactively by a human. Captures business needs as Requirement issues
in the agentic repo. No branch, no commit, no PR — the issue is the artefact.

1. Read context (see Session Initialisation)
2. Converse with the human to distil the requirement
3. Create a GitHub Issue in the agentic repo with `requirement` + `backlog` or `draft` label
4. Confirm the issue URL to the human

### Scoping Session (Phase 2)

Run interactively by a human. Decomposes a Requirement into Feature issues
in the relevant domain repo(s). No branch, no commit, no PR.

1. Read context (see Session Initialisation)
2. Read the target Requirement issue in full
3. Converse with the human to scope the Feature(s)
4. Create Feature issue(s) in the domain repo with `feature` + `backlog` label
5. Wire sub-issue relationship: Feature → parent Requirement (cross-repo)
6. Add Feature to org Project, set Domain field
7. When human confirms ready: apply `in-design` label → triggers Feature Design Session

### Feature Design Session (Phase 3)

Triggered automatically by GitHub Actions when a Feature issue is labelled `in-design`.
Runs in the domain repo context. Decomposes the Feature into Task sub-issues.

1. Read context (see Session Initialisation)
2. Read the Feature issue spec in full
3. Analyse the codebase to understand what exists and what must be built
4. Create Task sub-issues under the Feature issue (ordered by creation sequence)
5. Create the feature branch: `feature/N-description` where N is the Feature issue number
   — this auto-links the branch to the Feature issue
6. Apply `in-development` label on the Feature issue → triggers Dev Session
7. Exit cleanly — do not push files, do not open a PR

### Dev Session (Phase 4)

Triggered automatically by GitHub Actions when a Feature issue is labelled `in-development`.
Runs on the feature branch in the domain repo. Processes Task sub-issues to completion.

1. Read context (see Session Initialisation)
2. Query open Task sub-issues on the Feature issue, ordered by issue number
3. For each Task:
   - Implement the work described in the Task issue
   - Build and test — if either fails, stop immediately and exit with error
   - Commit: `feat: [task description] — task N of N (#feature-issue)`
   - Close the Task issue: `gh issue close <task-number>`
4. When all Tasks are closed — exit cleanly
5. The workflow handles: push, PR creation with `Closes #N`, applying `in-review` label

### Interactive Session

Run manually by a human, typically to investigate or recover from a workflow failure,
or for exploration and manual work outside the automated pipeline.

1. Read context (see Session Initialisation)
2. Confirm not on `main` before making any changes
3. Follow the same build/test discipline as Dev Session

### Foreground Recovery

When the GitHub Actions workflow fails (build red, tests failing, conflict), the human
will open an Interactive Session to diagnose and fix. The following rules apply:

- Query open Task sub-issues on the Feature issue before touching any code
- Diagnose the root cause from the exact error output — do not guess
- Fix only what is failing; do not expand scope or refactor surrounding code
- After fixing: build, test, commit, close the Task issue, and push
- Inform the human what was fixed
- If the automatic re-trigger does not start the workflow, apply `in-development`
  label again to re-trigger the Dev Session
- If the fix requires a contract change or broad refactor, stop and raise it before proceeding

---

## Git Rules

One branch per Feature. Tasks are commits on that branch, not separate branches.

**Rules:**
- Never commit or make changes on `main` — unconditional
- Never push from within a recipe — the workflow pushes after the recipe exits cleanly
- Never open a PR from within a recipe — the workflow handles this
- Never merge pull requests — leave that for human review
- **Always use `git mv` to rename or move tracked files** — never OS-level `mv`
- **Stage new files immediately** using `git add <file>` after creating them
- Branch names: `feature/N-description` where N is the Feature issue number
- Commit messages per task: `feat: [task description] — task N of N (#N)`
- PR title: `feat: [Feature issue title]`
- PR body: `Closes #N` where N is the Feature issue number

---

## Testing — Universal Rules

- Every piece of logic must have tests
- Tests must be executed and must pass — writing tests without running them
  does not satisfy this rule
- Tests must cover: success cases, failure cases, and edge cases
- Never claim a task complete with failing tests
- Fix failing tests before moving to the next step
- Unit tests must not require external services — isolate infrastructure dependencies
- See the relevant file in `base/standards/` for language-specific test commands,
  frameworks, naming conventions, and patterns

---

## Build Verification — Universal Rules

- The build must pass cleanly before claiming a task complete
- Report exact command output on any failure — diagnose before retrying
- See the relevant file in `base/standards/` for language-specific build commands

---

## Working Principles

- Analyse the full problem before modifying any code
- Prefer small, incremental changes over large rewrites
- When requirements are ambiguous, ask — never invent behaviour
- Correctness and maintainability take precedence over cleverness
- Do not make changes outside the scope of the current task
- Propose large refactors before implementing them — never execute without approval

---

## Sensitive Operations — Ask Before Proceeding

Always ask a human before:
- Deleting any file
- Broad refactors across multiple packages
- Changing public APIs
- Modifying core business logic (charging, payments, financial calculations)
- Introducing new dependencies
- **Modifying any contract** — see Contract Rules below

---

## Contract Rules

A **contract** is any structure or schema shared with an external system or process.
Contracts must **never be modified without explicit human approval**, regardless of
how minor the change appears.

The meta-rule: **You can never know all consumers of a contract.** A field that
appears unused may be read by a Java service, a database migration, a reporting
tool, or a downstream event processor. Adding, removing, or renaming fields
without approval is always a breaking change risk.

### What counts as a contract

**Kafka event schemas** — any struct that is serialised and published to a Kafka
topic, or deserialised from a Kafka topic. These are consumed by other services
that you cannot see. The schema is defined by the upstream publisher — the
consuming service must accept what it receives, not invent fields.

**Database-serialised structs** — any struct that is marshalled into a database
column (e.g. as JSON or JSONB). Other applications may read those columns directly.

**GraphQL schema** — any type, field, query, mutation, or subscription exposed via
the GraphQL API. External clients depend on these names and shapes.

**Store query interfaces** — sqlc-generated query interfaces. Modify the SQL, not the Go.

### Rules

1. **Never add, remove, or rename fields** on a contract struct without explicit
   human approval.

2. **Never invent fields** that the upstream publisher does not send.

3. **Internal IDs belong in internal structs**, not in contracts.

4. **When in doubt, ask.** Stop and raise it with the human before making any change.

5. **Document the reason** for any approved contract change in `DECISIONS.md`
   with an ADR, including which consumers were checked and what the migration plan is.

---

## Communication

- Explain what changed, referencing specific files, packages, and issue numbers
- Explain reasoning behind design decisions
- Explicitly highlight risks for changes touching critical business logic
- State clearly when a verification step could not be performed
- Prefer clarity over brevity when describing risks

---

## Task Lifecycle

**After each task completes (before moving to the next):**
1. Append significant decisions to `DECISIONS.md` in ADR format (if applicable)
2. Close the Task issue: `gh issue close <task-number> --repo <domain-repo>`
3. Commit: `feat: [task description] — task N of N (#feature-issue)`

**When all tasks are complete:**
1. Exit cleanly — do not push, do not open a PR
2. The workflow pushes and opens the PR automatically

**ADR format for DECISIONS.md:**
```
## ADR-NNN — Title
**Status:** Accepted
**Area:** Which part of the system
**Decision:** What was decided
**Rationale:** Why
**Consequences:** What this means going forward
```
