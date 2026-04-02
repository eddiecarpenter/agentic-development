# agentic-development

A framework for AI-assisted software development using an agentic SDLC process.
Fork this repo to bootstrap your own agentic development environment.

## What this is

This is a GitHub template repository that provides:
- A structured agentic development process (requirements → scoping → design → dev)
- Global protocol rules in `base/AGENTS.md`
- Language standards in `base/standards/`
- Reusable GitHub Actions workflows in `.github/workflows/`

## How to use

### Bootstrap a new agentic environment
```bash
gh repo create my-project-agentic --template eddiecarpenter/agentic-development
```

Then run the bootstrap session to configure the environment for your project.

### Sync updates from this template
Periodically pull updates from this repo into your agentic environment:
```bash
# In your agentic repo
git clone git@github.com:eddiecarpenter/agentic-development.git /tmp/agentic-dev-sync
cp -r /tmp/agentic-dev-sync/base/ ./base/
rm -rf /tmp/agentic-dev-sync
# Review changes, then commit
```

Only `base/` is managed by the template. All local files (`AGENTS.local.md`, `REPOS.md`, etc.) are never touched by a sync.

## Structure

```
base/                    ← managed by template, never manually edited
  AGENTS.md              ← global protocol rules
  standards/
    go.md
    java.md
.github/
  workflows/             ← reusable workflow definitions
CLAUDE.md                ← entry point — loads base + local
AGENTS.local.md          ← local overrides and project-specific rules
REPOS.md                 ← repository registry
```

## Two-layer rules

- `base/AGENTS.md` — global rules, synced from this template
- `AGENTS.local.md` — local overrides, never overwritten by sync

`CLAUDE.md` loads both in order. Local rules take precedence.
