# Capture Feature — Issue Body Template

## Purpose

Defines the canonical structure for Feature issues created during Scoping Sessions (Phase 2).
Apply this skill when writing the body of every Feature issue.

## Template

```markdown
## User Story

As a <role>, I want <goal>, so that <benefit>.

## Acceptance Criteria

Each criterion is a Given/When/Then scenario. Cover at minimum:
- One success case
- One failure case
- At least one edge case

- **Given** <precondition>,
  **when** <action>,
  **then** <expected outcome>

- **Given** <precondition>,
  **when** <action fails or input is invalid>,
  **then** <expected failure behaviour>

- **Given** <edge-case precondition>,
  **when** <action>,
  **then** <expected edge-case outcome>

## Notes

Implementation constraints, API choices, or technical context known at scoping time.
Keep this separate from acceptance criteria — criteria define outcomes, notes capture context.

Omit this section if there is nothing to note.

## Parent

Closes part of #<requirement>
```

Use `Closes #<requirement>` instead of `Closes part of` when the requirement produces only a single feature.

## Rules

- User Story is mandatory — every feature issue must include one
- Acceptance criteria must use Given/When/Then format — not checkboxes, not prose
- Minimum three criteria: success, failure, edge case — add more as needed
- Notes capture context — never mix implementation detail into acceptance criteria
- Parent link is mandatory — every feature traces back to a requirement
