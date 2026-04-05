# Release — Publish a new template version

## Purpose

Publish a new versioned release of the `agentic-development` template so downstream
repos can sync the latest `base/` changes via `gh agentic sync`.

## When to Run

When a meaningful set of changes has been merged to `main` and you want to make them
available to downstream repos. Not every merge needs a release — batch related changes
into a coherent version.

## Process

1. Review merged PRs since the last release:
   ```bash
   gh release view --repo <template-repo>   # see last release
   gh log --oneline <last-tag>..HEAD        # see commits since
   ```

2. Decide the next version using semantic versioning:
   - `fix:` commits only → **patch** bump (v0.1.0 → v0.1.1)
   - `feat:` commits → **minor** bump (v0.1.0 → v0.2.0)
   - Breaking changes (`BREAKING CHANGE:` in commit footer) → **major** bump (v0.1.0 → v1.0.0)

3. Create the release:
   ```bash
   gh release create vX.Y.Z --generate-notes --target main
   ```
   GitHub auto-generates release notes from merged PRs since the last tag.

4. Confirm the release is published:
   ```bash
   gh release list --repo <template-repo> --limit 3
   ```

## Notes

- There is no automated release workflow — this is a deliberate human action
- `TEMPLATE_VERSION` in the template repo itself is not updated — the git tag is the version
- Downstream repos track the version they last synced in their own `TEMPLATE_VERSION` file,
  updated by `gh agentic sync`
