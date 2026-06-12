# Incubator Agent Harness

This repository is a lightweight incubator for project ideas. Keep changes small and limited to the idea registry unless a human explicitly asks for more.

## Required workflow

- Append a dated entry to `ideas.md` for each new idea.
- Append a matching object to the `ideas` array in `ideas.json` for each new idea.
- Do not create directories for ideas or prototypes unless explicitly asked.
- Submodule lifecycle is managed by `.github/workflows/reconcile-submodules.yml` (links new idea repos) and Dependabot (bumps pinned SHAs). Do not add or bump submodules by hand.
- Do not touch `template/` unless explicitly asked.
- Keep `ideas.md` and `ideas.json` synchronized.

## Idea schema

`ideas.json` contains a top-level object with an `ideas` array. Each idea object has:

- `name`: machine-friendly project name, usually kebab-case.
- `description`: one-sentence project summary.
- `created`: creation date in `YYYY-MM-DD` format.
