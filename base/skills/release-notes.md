# Release Notes

## Purpose

Generate human-readable, well-structured release notes from git commit history.
This skill is invoked by the Release recipe when a version tag is pushed to `main`.

---

## What Makes Good Release Notes

Release notes are read by humans — developers integrating the framework, product owners
deciding whether to sync, and contributors reviewing what changed. They are not a raw
commit log. They answer: **what changed, and why does it matter?**

Good release notes:
- Lead with a one-sentence summary of what the release achieves overall
- Group changes by type so readers can scan for what matters to them
- Use plain language — no jargon, no internal abbreviations
- Omit noise: merge commits, version bumps, CI fixes, and formatting-only changes
  unless they have direct user impact
- Name the thing that changed, not the mechanism: "Feature switches now support
  permanent-disable mode" not "Added `permanent_disable` field to FeatureSwitch struct"

---

## Format

```markdown
<One sentence summary of what this release delivers overall.>

## Features
- <What the feature does and why it matters>
- ...

## Fixes
- <What was broken and what is now correct>
- ...

## Documentation
- <What was documented or clarified>
- ...

## Chores
- <Infrastructure, dependency updates, tooling — only if notable>
- ...
```

Rules:
- Omit any section that has no entries
- Do not include the release tag or a title — GitHub adds the title separately
- Each bullet is one sentence, present tense: "Adds...", "Fixes...", "Removes..."
- If a change affects downstream repos (breaking change, new required secret, new
  convention), call it out explicitly in the relevant bullet

---

## Commit Categorisation

| Commit prefix | Section |
|---|---|
| `feat:` | Features |
| `fix:` | Fixes |
| `docs:` | Documentation |
| `chore:`, `ci:`, `refactor:`, `style:`, `test:` | Chores (only if notable) |
| Merge commits, `bump:`, `sync:` | Omit |

When commit messages are ambiguous, use the changed files to infer intent.

---

## What to Omit

- Merge commits (`Merge pull request #N from ...`)
- Automated commits (`chore: update TEMPLATE_VERSION`, `chore: bump to x.y.z-SNAPSHOT`)
- Minor CI/tooling changes with no user impact
- Formatting-only changes
- Commits that duplicate another commit in the same release

---

## Release Notes for Framework Releases Specifically

This framework is used by downstream repositories. When writing release notes for
`ai-native-delivery` releases, flag any changes that require downstream action:

- New required secrets or variables → call out explicitly
- Changes to `base/AGENTS.md` rules that affect agent behaviour → summarise the rule change
- New or renamed skills → note the skill name
- Breaking changes to workflow triggers or recipe parameters → describe migration

These are the changes downstream owners most need to know about before running
`gh agentic sync`.
