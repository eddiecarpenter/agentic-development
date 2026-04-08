# Project Brief — ai-native-delivery

## What this is

The `ai-native-delivery` framework is a template and protocol layer for AI-assisted
software delivery. It defines the rules, skills, workflows, and tooling conventions
that AI agents follow when working on any project that uses the framework.

The framework covers the full Continuous Delivery pipeline from requirements capture
through to a versioned, tagged release. Continuous Deployment (loading to production)
is out of scope and remains the responsibility of individual projects.

## Topology

This repo is the **Organisation control plane** for the ai-native-delivery ecosystem:

| Repo | Type | Role |
|---|---|---|
| `eddiecarpenter/ai-native-delivery` | Template / control plane | Defines global agent protocol, standards, and CI workflows |
| `eddiecarpenter/gh-agentic` | Tool | GitHub CLI extension — bootstraps and manages agentic environments |

## What is built here

- `base/AGENTS.md` — the global agent rulebook, consumed by all downstream projects
- `base/skills/` — playbooks for each pipeline session type
- `base/standards/` — language-specific build, test, and coding standards
- `base/.github/workflows/` — the agentic pipeline workflow definitions
- `base/concepts/` — architectural concepts and delivery philosophy
- `base/docs/examples/` — annotated examples for downstream projects

## How changes flow

Changes to the framework are developed here using the same SDLC pipeline the
framework defines. Once merged and tagged, downstream projects pull the changes
via `gh agentic sync`.

## Key conventions

- `base/` is read-only for AI agents in downstream projects — changes must originate here
- `AGENTS.local.md` in each downstream project holds project-specific overrides
- Template version is tracked in `TEMPLATE_VERSION` and updated automatically on each release
- Releases are triggered by `git tag vX.Y.Z && git push origin vX.Y.Z`
