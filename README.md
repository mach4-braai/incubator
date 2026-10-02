# Incubator

This repository holds a lightweight register of early project ideas before they become standalone repositories or provisioned services.

## Local setup

Each idea is a git submodule pointing at its own repository. Clone with submodules and run the init task so each submodule lands on its tracking branch:

```sh
git clone --recurse-submodules git@github.com:mach4-braai/incubator.git
cd incubator
mise run init
```

For an existing clone where submodules were never initialized, run `mise run init` from the incubator root.

## Idea registry

[ideas.json](ideas.json) is the registry. Each idea is one entry with a name, a description and a creation date. Adding an entry there creates the GitHub repository and links it here as a submodule.

## Projects

| Project | Description |
|---|---|
| [agent-conductor](https://github.com/mach4-braai/agent-conductor) | Claude Code skill where Opus plans, DeepSeek agents build in Docker, and a merge agent lands the work. |
| [ai-subscription-wallet](https://github.com/mach4-braai/ai-subscription-wallet) | Company AI budgets with per-employee limits, a self-serve tool catalog and one invoice. |
| [anki-card-linter](https://github.com/mach4-braai/anki-card-linter) | Anki add-on that uses an LLM to flag badly written cards without writing them. |
| [attentiond](https://github.com/mach4-braai/attentiond) | Local daemon that tracks what needs your attention across tools and serves it over HTTP. |
| [family-cloud](https://github.com/mach4-braai/family-cloud) | Self-hosted Nextcloud on Hetzner for about ten family users. |
| [gauger](https://github.com/mach4-braai/gauger) | GitHub Action that samples runner CPU and memory per step and streams it to gauger-server. |
| [gauger-server](https://github.com/mach4-braai/gauger-server) | Self-hosted server and web UI that joins GitHub Actions job timings with gauger runner metrics. |
| [github-actions-agent-swarm](https://github.com/mach4-braai/github-actions-agent-swarm) | Local orchestrator that hands sub-tasks to GitHub Actions workflows running as short-lived agents. |
| [homebrew-tap](https://github.com/mach4-braai/homebrew-tap) | Homebrew tap for the incubator CLIs, bumped by each project's release workflow. |
| [hum](https://github.com/mach4-braai/hum) | Daemon and CLI that turn work-session events into an ambient musical soundscape. |
| [idv-knowledge-roadmap](https://github.com/mach4-braai/idv-knowledge-roadmap) | Interactive roadmap from beginner to semi-expert in identity verification and digital trust. |
| [k8s-whisper-homelab](https://github.com/mach4-braai/k8s-whisper-homelab) | Kubernetes home lab with a Raspberry Pi node running Whisper speech-to-text. |
| [learning-graph](https://github.com/mach4-braai/learning-graph) | Agent that builds an Obsidian knowledge graph from Claude sessions, conversations and articles. |
| [moxer](https://github.com/mach4-braai/moxer) | Text-only terminal boxing game that lets you feel like a boxer without getting hit. |
| [pixel-sling](https://github.com/mach4-braai/pixel-sling) | Minimalist browser physics game where you swing and release a shepherd's sling. |
| [sky-spotter](https://github.com/mach4-braai/sky-spotter) | Real-time flight tracker with a compass pointer and incoming flight predictions. |
| [sumo-analytics](https://github.com/mach4-braai/sumo-analytics) | Sumo dashboard with wrestler stats, tournament results and technique analytics. |
| [value-lens](https://github.com/mach4-braai/value-lens) | Value investing tool that searches SEC filings and summarises them with Claude. |
| [voice-dictation](https://github.com/mach4-braai/voice-dictation) | Real-time voice dictation with a Go server, TEN VAD and swappable speech-to-text backends. |
| [whatsapp-sidecar](https://github.com/mach4-braai/whatsapp-sidecar) | Read-only local app that mirrors WhatsApp chats in a web UI with AI summaries. |

## Project template

The `template/` directory contains the minimal starter files for a future project: README, MIT license template, and OS-only ignore rules. Do not modify it unless a task explicitly asks for template changes.

## Provisioning

Infrastructure and provisioning workflows live in [mach4-braai/infra](https://github.com/mach4-braai/infra) (private). It also manages the GitHub settings of every repository listed above.
