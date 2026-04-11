# Local Rules

This file contains project-specific rules and overrides that extend or
supersede the global protocol defined in `.ai/RULEBOOK.md`.

This file is optional. If it does not exist, no local rules are applied.

---

## GitHub Actions Sync

This is the `ai-native-delivery` template repo. Unlike downstream repos, there is no
upstream to sync from — the **pipeline workflows** in `.github/workflows/` must be kept
in sync with their counterparts in `.ai/.github/workflows/` manually.

### Pipeline workflows (sync both ways)

These workflows operate this repo and must exist in both locations:

| File | Purpose |
|---|---|
| `agentic-pipeline.yml` | Runs the agentic delivery pipeline |
| `release.yml` | Generates AI release notes on publish |

**After any change to a pipeline workflow in `.ai/.github/workflows/`:**
```bash
cp .ai/.github/workflows/<changed-file>.yml .github/workflows/<changed-file>.yml
git add .github/workflows/<changed-file>.yml
git commit -m "chore: sync <changed-file>.yml from .ai/"
```

### Distribution-only workflows (`.ai/` only — do NOT copy to `.github/workflows/`)

Some files in `.ai/.github/workflows/` are **templates distributed to downstream repos**
via `gh agentic sync`. They are not intended to run on this template repo and must
**not** be copied to `.github/workflows/`:

| File | Purpose |
|---|---|
| `build-and-test.yml` | Go project build/test template |

Check pipeline workflow drift at any time:
```bash
diff agentic-pipeline.yml release.yml | xargs -I{} diff .ai/.github/workflows/{} .github/workflows/{}
```
Or manually:
```bash
diff .ai/.github/workflows/agentic-pipeline.yml .github/workflows/agentic-pipeline.yml
diff .ai/.github/workflows/release.yml .github/workflows/release.yml
```

---

## Local Skills

Local skills for this repo live in `skills/`. See `skills/release.md` for the release process.
