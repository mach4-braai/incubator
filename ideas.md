# Incubator Ideas

All notable ideas are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/).

## [Unreleased]

### 2026-06-10

#### Moxer

A text-only terminal boxing game that lets the player feel a simplified version of being a boxer without being hit in the face.

**Concept:** The proof of concept is a near-real-time terminal game built around tiny boxing exchanges. The player first chooses what to watch — shoulders or feet — to receive an ambiguous hint, then must choose one of four actions before a timer expires: `Block left`, `Block right`, `Left Hook`, or `Right Hook`. The opponent has a hidden loading/intent phase that drives both the hint and the final action, and the result is narrated in concise text.

**Key points:**

- Working v0 name: Moxer — mock + boxer.
- Core fantasy: manage boxer-like tactical overload without physical risk.
- POC move set: `Block left`, `Block right`, `Left Hook`, `Right Hook`.
- Scouting split: shoulders hint at punch direction/preparation; feet hint at commitment, balance, and counter opportunity.
- Core loop: opponent intent → player scouting choice → timed player action → concise simultaneous-resolution narration.
- Keep v0 minimal; later expansion can add feints, straight punches, uppercuts, body blocks/shots, range, rhythm, and richer opponent patterns.

### 2026-05-27

#### Learning Graph

A personal knowledge graph that an AI agent builds over time from your
work sessions, grill-me conversations, and personal articles. Seeded
from curated software engineering references, it grows to map your
zone of proximal development (ZPD) — not as a skill checklist, but
as a thinking-level diagnosis: where are you reasoning at the
operational layer vs. the architectural layer?

**Concept:** Claude Code sessions, grill-me sessions, and personal
articles accumulate as raw signal. A periodic agent analyzes new
inputs and updates an Obsidian vault — markdown nodes connected by
wiki-links, forming a concept graph. A one-time agent interview
bootstraps the baseline from your SRE/platform engineering background.
The agent produces a periodic learning digest (big picture) and a
prioritized learning queue (what to study next). The output targets
design thinking and domain knowledge — the skills that gain leverage
as agentic development commoditizes implementation.

**Key points:**

- Obsidian vault as storage: markdown skill nodes + wiki-link concept
  graph, zero infrastructure, human-readable
- Seeds: SWEBOK, Kleppmann's *Designing Data-Intensive Applications*,
  Google SRE Book, select distributed systems papers — curated, not
  exhaustive; the rest emerges from sessions
- Bootstrap: one-time agent interview mapping SRE/platform baseline,
  targeted at identifying design thinking gaps
- Periodic cron agent analyzes new inputs and updates the vault
- ZPD framing: thinking-level diagnosis ("you reason at the
  operational layer but lack design vocabulary") rather than flat
  skill gaps
- Output: learning digest + learning queue; grill-me sessions on new
  reading naturally work through the queue
- Personal articles section: write what you learned, agent absorbs
  into the knowledge graph
- Future: Postgres + pgvector layer on top of the vault for richer
  agent querying

### 2026-05-14

#### Agent Conductor

A Claude Code skill that uses Claude Opus 4.7 (inside the active Claude
Code session) to plan work, then dispatches the implementation to DeepSeek
v4 agents running in isolated Docker containers. A final merge agent —
Claude itself, with full planning context — lands all work back onto the
user's main branch.

**Concept:** The user invokes `/agent-conductor <task>` in Claude Code.
Opus clarifies requirements, decomposes the work into a structured plan
written to `/tmp/plans/<slug>.md`, and (after approval) calls
`scripts/dispatch.sh`. The dispatcher reads the plan, creates a git
worktree per independent task off the current branch, and runs one Docker
container per task. Inside each container a small Go agent calls DeepSeek
v4 via its OpenAI-compatible API, executes tool calls (`read_file`,
`write_file`, `bash`, `finish`) against `/workspace`, and commits the
result to its task branch. Once all executors finish, `merge.sh` sequences
task branches back onto the user's main branch in dependency order. On
conflict, `merge-conflicts.json` is written and the skill hands conflict
resolution back to Opus, which resolves with full plan context and
re-runs the merge.

**Key points:**

- Claude Code skill: Opus 4.7 plans, DeepSeek v4 implements — each in
  its natural strength
- Plans stored in `/tmp/plans/<slug>.md` with YAML frontmatter (name,
  parallelism, workspace) + structured task list
- `parallelism: auto` heuristic: tasks with no `depends_on` and disjoint
  file lists fan out concurrently; others run serially
- Each executor container mounts the repo as `/workspace` and gets a
  read-only plan task at `/plan/task.md`
- Go agent hard caps: `--max-iterations 30`, `--timeout 15m`;
  writes `/plan/status.json` between iterations
- Merge agent = Opus (subscription, no extra API cost) — resolves
  conflicts using full plan + per-task status context
- DeepSeek v4 via official API (`DEEPSEEK_API_KEY`); no self-hosting
- Motivation: use Opus's reasoning "for free" via subscription, pay only
  DeepSeek's token costs for raw implementation

### 2026-04-22

#### Family Cloud

A self-hosted Nextcloud deployment on Hetzner replacing Google Drive
for ~10 extended family users. Infrastructure-as-code with OpenTofu,
Docker Compose for services, S3-backed primary storage.

**Concept:** A single Hetzner CX32 VPS running Nextcloud 29+ in
Docker with Postgres 16, fronted by Caddy for automatic TLS. Primary
file storage lives on Hetzner Object Storage (S3-compatible) for
cost-effective scaling to 5TB. Tailscale provides secure admin
access. All infrastructure managed by OpenTofu with secrets handled
via SOPS+age.

**Key points:**

- Hetzner CX32 VPS + Object Storage (S3) for primary file storage
- Nextcloud 29+ in Docker, Postgres 16, Redis for caching
- Caddy reverse proxy with automatic HTTPS
- Tailscale for admin/SSH access
- OpenTofu for infrastructure provisioning (VPS, DNS, object storage)
- SOPS + age for secret management
- cloud-init for initial server bootstrap
- ~€38/mo vs ~€100/mo for Google One across 10 users
- Motivation: cost savings + data sovereignty (user relocating to
  Germany)
- Known risk: Nextcloud S3 primary storage has issues with large
  file uploads (>80MB) and broken server-side encryption — plan
  includes mitigations with escape hatch to local disk

### 2026-04-07

#### Value Lens

A RAG-powered financial analysis tool for value investing. Ingests SEC
10-K filings, chunks and embeds them into pgvector, and provides a
query interface backed by Claude for synthesized answers with source
citations.

**Concept:** A Go backend fetches 10-K filings from SEC EDGAR for
tracked companies (TSLA, AAPL, GOOGL, NFLX, META), parses HTML into
logical sections (Business, Risk Factors, MD&A), chunks them, embeds
via self-hosted Ollama (nomic-embed-text), and stores vectors in
Postgres+pgvector. A query endpoint performs vector similarity search,
retrieves relevant chunks, and synthesizes answers via Claude API. A
thin React+Bun frontend provides the UI.

**Key points:**

- Go backend with chi router, pgx v5, pgvector-go
- Postgres 16 + pgvector for structured data and vector storage
- Ollama + nomic-embed-text for self-hosted embeddings
- SEC EDGAR API for free, legal access to 10-K filings
- Claude API (anthropic-sdk-go) for RAG query synthesis
- React + Bun frontend
- CLI ingest command for per-company or batch ingestion
- Docker Compose for local development (Postgres, Ollama)

### 2026-03-06

#### Sky Spotter

A mobile app that shows aircraft flying near you in real-time. Tap a
flight to get a compass arrow pointing at it in the sky, scroll down
for flight details. Includes a predictive "incoming flights" table.

**Concept:** React Native + Expo app using the ADSB.lol API (free,
no auth) to display nearby aircraft. A compass overlay arrow (2D, no
camera AR) points toward selected flights. Incoming flights are
predicted by analyzing heading and speed of aircraft outside the
user's configurable radius (default 15km).

**Key points:**

- React Native with Expo managed workflow, TypeScript
- ADSB.lol API for real-time ADS-B aircraft data (ODbL 1.0)
- Compass pointer using device magnetometer + accelerometer
- Predictive incoming flights table with ETA calculation
- In-app notifications only (no background polling for v1)
- Dark theme optimized for outdoor/dusk use

### 2026-02-08

#### Sumo Analytics

A sumo wrestling analytics dashboard. Users view wrestler statistics,
tournament results, head-to-head records, and technique analytics
powered by data from the open sumo-api.com API.

**Concept:** A Grafana/Datadog-style dark analytics dashboard for sumo
wrestling data. A fetcher service syncs data from sumo-api.com into
PostgreSQL, and a Next.js frontend renders server-side analytics pages
with charts and data tables.

**Key points:**

- Next.js App Router with Tailwind v4, dark analytics theme
- Drizzle ORM with PostgreSQL for type-safe queries
- Recharts for SVG-based data visualization
- node-cron fetcher with dynamic scheduling (daily/hourly during basho)
- npm workspaces monorepo: @sumo/db, @sumo/web, @sumo/fetcher
- Managed via mise (Node.js runtime + task runner)

### 2026-02-03

#### Pixel Shepherd Sling

A minimalist browser-based physics game inspired by Foddy's Cricket. Hold
SPACE to swing a stone overhead, building momentum with each rotation,
then release to throw. The stone arcs, bounces, and rolls across a flat
plain until it rests. No score, no progression - pure zen.

**Concept:** A shepherd stands on the left side of the screen. The player
holds SPACE to spin a stone on a sling, with each rotation building speed.
Releasing SPACE launches the stone tangentially. Physics handles the rest -
ballistic arc, bounces, rolling friction. Press R to reset and throw again.

**Key points:**

- Phaser.js 3 with Matter.js physics, Vite + TypeScript
- Single-button control: SPACE to swing/release, R to reset
- Momentum-based mechanics: rotations build launch velocity
- Stark minimalist pixel art (5-color palette)
- No UI, no score, no progression - meditative experience
- Inspired by Foddy's Cricket

### 2026-02-02

#### WhatsApp Sidecar

A local sidecar app that mirrors WhatsApp conversations in a web UI
with AI-generated summaries. Read-only with respect to WhatsApp —
it observes but never sends.

**Concept:** A Go backend connects to WhatsApp as a linked device via
`whatsmeow`, stores messages in SQLite, and periodically generates
conversation summaries using a local LLM (GLM-4 via Ollama). A
Next.js frontend displays conversations and summaries. Everything
runs locally — no data leaves your machine.

**Key points:**

- Go backend with `whatsmeow` for WhatsApp multidevice API
- SQLite for message and summary storage
- Ollama with GLM-4 for local AI inference
- Periodic rolling summaries (default every 6 hours)
- Next.js web UI (App Router, Tailwind CSS)
- Project managed via `mise` (`mise run ui`, `mise run backend`, etc.)

### 2026-01-29

#### Voice Dictation Server

A real-time voice dictation system: speak and text appears in the
active text field on macOS. A Go server handles voice activity
detection (TEN VAD), then dispatches speech segments to a swappable
STT backend over gRPC.

**Concept:** A native macOS client captures mic audio and streams it
to a Dockerized Go server via ConnectRPC bidirectional streaming.
The server runs TEN VAD to segment speech from silence, then sends
speech-only chunks to an STT service implementing a shared gRPC
contract. Transcribed text streams back to the client, which injects
it into the currently focused text field via clipboard + simulated
paste.

**Key points:**

- macOS native Go client (mic capture, text injection via CGEvent)
- Go server with TEN VAD (C library, Go bindings) in Docker
- STT backends as separate Docker containers behind a shared
  protobuf `STTService` contract
- Two initial backends: whisper.cpp (Go) and WhisperX (Python)
- ConnectRPC bidi streaming between client and server
- gRPC unary calls between server and STT (VAD pre-segments audio)
- Docker Compose with profiles to select STT backend
- Swappable backends via config — just change the STT address

### 2026-01-28

#### GitHub Actions as Local Agent Swarm

Run a local coding session where a coordinator agent dispatches work to
GitHub Actions workflows acting as sub-agents. Each workflow runs on its
own runner as an isolated, ephemeral worker.

**Concept:** A local agent receives a high-level task, decomposes it,
and fans out sub-tasks to purpose-built GitHub Actions workflows on a
public repository. Each workflow is a disposable agent with its own
runner environment — e.g. a research agent that clones repos, reads
docs, or probes APIs, then reports findings back.

**Key points:**

- Local agent acts as orchestrator / dispatcher
- GitHub Actions workflows serve as remote sub-agents (research, build,
  test, analysis, etc.)
- Each agent runs on a GitHub-hosted runner — full VM, ephemeral, free
  for public repos
- Communication via workflow dispatch events and artifact / output
  retrieval
- Public repo created specifically to host these agent workflows
- Viability is not the concern right now — this is an idea to explore

#### Kubernetes Whisper Homelab

A multi-node Kubernetes home lab with a Raspberry Pi edge node
running a lightweight Whisper model for speech-to-text.

**Concept:** A learning-focused home lab project. Run a K8s cluster
(likely k3s) on a primary server, with a Raspberry Pi joined as a
worker node. The Pi runs a small Whisper model (e.g. `whisper.cpp`
with `tiny` or `base`) as a dedicated STT edge device. The term for
this is an **edge computing** setup — the Pi acts as an edge node
performing inference close to the input source.

**Key points:**

- K8s cluster on home server (k3s is ideal — lightweight, ARM-native)
- Raspberry Pi joins the cluster as a worker node (yes, fully
  supported — k3s runs natively on Pi's ARM architecture)
- Pi runs a small Whisper model optimised for CPU inference
  (whisper.cpp, faster-whisper, etc.)
- K8s schedules the STT workload onto the Pi via node selectors or
  taints/tolerations
- Speech-to-text accessible as a service across the local network
- Primary goal is hands-on learning — K8s multi-node clusters, edge
  computing, ML model serving on constrained hardware
- Self-hosted alternative to cloud STT APIs

