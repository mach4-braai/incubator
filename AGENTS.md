# Incubator Agent Harness

This repository is a lightweight incubator for project ideas. Keep changes small and limited to the idea registry unless a human explicitly asks for more.

## Required workflow

- Register each new idea by appending a matching object to the `ideas` array in `ideas.json`. This file is the single source of truth for the idea registry.
- Do not create directories for ideas or prototypes unless explicitly asked.
- Submodule lifecycle is managed by `.github/workflows/reconcile-submodules.yml` (links idea repos that already exist on GitHub) and Dependabot (bumps pinned SHAs). Do not add or bump submodules by hand.
- Do not touch `template/` unless explicitly asked.

## Idea schema

`ideas.json` contains a top-level object with an `ideas` array. Each idea object has:

- `name`: machine-friendly project name, usually kebab-case.
- `description`: one-sentence project summary.
- `created`: creation date in `YYYY-MM-DD` format.

## Agent skills

### Issue tracker

Issues live in GitHub at `mach4-braai/incubator`, accessed via the `gh` CLI. See `docs/agents/issue-tracker.md`.

### Triage labels

Canonical five-role vocabulary, using the default label names. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context: `CONTEXT.md` and `docs/adr/` at the repo root. See `docs/agents/domain.md`.
