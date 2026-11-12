---
document_type: architecture
content_type: descriptive
summary: >-
  The repository is a single-service, polyrepo-style Python project — not a
  monorepo. No workspace tool (uv workspaces, Poetry workspaces, Nx, Turborepo,
  etc.)...
last_updated: '2026-11-12T19:54:21.420Z'
tags:
  - architecture
  - topology
  - python
  - jupyter-/-ipykernel
---
# Architecture

## Monorepo / Repository Shape

The repository is a single-service, polyrepo-style Python project — not a monorepo. No workspace tool (uv workspaces, Poetry workspaces, Nx, Turborepo, etc.) is in play; dependencies are declared once in the root `pyproject.toml`, which carries `[tool.uv] package = false` so `uv sync` installs pinned floor dependencies into `.venv` without building the project itself. Source control is GitHub.

Top-level layout groups training materials under per-day directories, with a bootstrap script and environment template at the root:

| Path | Role |
| ---- | ---- |
| `basecamp_setup.py` | Repo-root bootstrap: installs uv, builds `.venv`, registers the Jupyter kernel, prompts for the API key, writes `.env`, probes TLS. |
| `pyproject.toml` | Dependency manifest (`package = false`). |
| `.env.example` | Template documenting every configuration option. |
| `day1/` | Day-one exercises (notebooks, scripts, per-folder READMEs). |
| `day2/` | Day-two exercises (notebooks, scripts, per-folder READMEs). |
| `day{N}/{NN_exercise-name}/` | One exercise per folder: a `.ipynb` and/or `.py`, a `README.md`, optional supporting markdown, optional per-folder `CLAUDE.md` that overrides repo-level agent notes within that folder. |

File-placement patterns inside each exercise folder:

| Pattern | Location | Example |
| ------- | -------- | ------- |
| Exercise notebook | `day{N}/{NN_exercise-name}/{Notebook_Name}.ipynb` | `day1/02_developer-platform/Developer_Platform.ipynb` |
| Exercise Python script | `day{N}/{NN_exercise-name}/{Script_Name}.py` | `day1/04_diagnosing-ai-problems/Diagnose_Fix_Brief.py` |
| Exercise README | `day{N}/{NN_exercise-name}/README.md` | `day1/03_prompt-rescue/README.md` |
| Supporting markdown | `day{N}/{NN_exercise-name}/{document}.md` | `day1/04_diagnosing-ai-problems/diagnostic-framework.md` |
| Per-folder agent instructions | `day{N}/{NN_exercise-name}/CLAUDE.md` | `day1/04_diagnosing-ai-problems/CLAUDE.md` |

## Service Inventory

The repository contains a single library-type service with no runtime port.

| id | type | language | port | Role |
| -- | ---- | -------- | ---- | ---- |
| [[Basecamp-Exercises]] | library | Python (>=3.10) | — (library — no runtime) | Training exercise notebooks and scripts for Partner Basecamp using the Anthropic SDK. |

## Service Communication

The repository hosts one service and ships no inter-service edges. [[Basecamp-Exercises]] is a library of notebooks and scripts; there are no in-repo callers or callees. The only outbound traffic is from exercise code to the Anthropic API (see **External Integrations**).

Data-shape references between internal services: (not determined by analysis) — there are no internal services beyond [[Basecamp-Exercises]].

## External Integrations

| Vendor | SDK | In-repo client wrapper | Auth mechanism | Purpose | Environments served |
| ------ | --- | ---------------------- | -------------- | ------- | ------------------- |
| Anthropic | `anthropic[bedrock]>=1.6` | `basecamp_setup.py` (reads `.env`, exposes `connect()`) | `ANTHROPIC_API_KEY` in `.env` (gitignored); Bedrock and gateway-key variants are documented in `.env.example` and `SETUP.md` | LLM provider for exercise notebooks and scripts | (not determined by analysis) |

Notebooks never embed keys directly. The first code cell in each notebook calls `connect()` from `basecamp_setup.py`, which reads `.env` fresh on every invocation via `python-dotenv`. The `ANTHROPIC_MODEL` variable in `.env` selects the workhorse model used across notebooks (`MODEL`), defaulting to `claude-sonnet-5`; the Haiku-tier and Opus-tier comparison models stay fixed inside the exercises.

## Authentication & Authorisation

There is no end-user authentication flow: the repository is a local training library, not a hosted service. The only credential in play is the Anthropic API key (or a Bedrock / gateway-key equivalent), which authenticates outbound SDK calls to Anthropic.

Credential lifecycle:

1. `basecamp_setup.py` prompts the participant for the key in a visible terminal window. AGENTS.md instructs AI assistants to launch the script in a new OS terminal rather than their own shell, because the prompt is interactive and the assistant's shell cannot forward the user's keystrokes.
2. The key is written to `.env` at the repo root. `.env` is gitignored; `.env.example` is the committed template.
3. On every exercise run, `connect()` re-reads `.env` through `python-dotenv` so subsequent edits to `.env` take effect without restarting the kernel.
4. The Anthropic SDK (`anthropic[bedrock]>=1.6`) presents the key to the Anthropic API. No tokens are minted, exchanged, or validated inside this repository.

Per-service auth posture: (not determined by analysis) — [[Basecamp-Exercises]] is a library with no HTTP surface, so no auth middleware is declared.

Grounded step-by-step auth walk with `source_file:source_line` references: (not determined by analysis).

## Request Lifecycle

There is no HTTP request surface in this repository — [[Basecamp-Exercises]] is a library of notebooks and scripts. The closest analogue is the notebook/script execution path, which is not a request lifecycle:

- Participant opens a notebook under `day{N}/{NN_exercise-name}/` using the **Anthropic Basecamp (.venv)** Jupyter kernel.
- The first code cell calls `connect()` from `basecamp_setup.py`; `connect()` loads `.env`, honours `BASECAMP_TLS` for the certificate source, and returns an authenticated Anthropic client.
- Subsequent cells issue SDK calls to the Anthropic API using the shared model alias from `ANTHROPIC_MODEL`.

Per-surface step lists with `file:line` citations: (not determined by analysis).

## Data Architecture

[[Basecamp-Exercises]] owns no operational data stores. There are no databases, caches, queues, object stores, or emulators in play.

| Technology | Type | Port | Ownership |
| ---------- | ---- | ---- | --------- |
| (none) | — | — | — |

Schema-management approach: (not determined by analysis) — there is no database, so no migration tooling applies. The only persistent on-disk state the exercises produce is `.env` at the repo root (configuration, gitignored) and the `.venv/` virtual environment (installed artefacts, gitignored).

## Deployment Topology

(not determined by analysis) — no deploy-prefixed automation targets, deployment manifests, or hosted infrastructure are described for this repository. The repository ships as source that participants clone and run locally; nothing in this repo deploys to a shared environment.

## Local Development

The full local stack is a Python virtual environment plus a Jupyter kernel — there is no compose file, emulator fleet, or long-running service to start.

Primary flow, driven by `basecamp_setup.py` at the repo root (from `README.md § Setup & troubleshooting`):

| Step | Command | Effect |
| ---- | ------- | ------ |
| 1 | `python3 basecamp_setup.py` (macOS / Linux) · `py basecamp_setup.py` (Windows) | Installs [uv](https://docs.astral.sh/uv/) if missing, creates `.venv` with all pinned floor dependencies, registers the **Anthropic Basecamp (.venv)** Jupyter kernel, prompts for and saves the API key to `.env`, runs a connection check. |
| 2 | `python3 basecamp_setup.py --check-tls` | Probes which certificate source reaches the Anthropic endpoint from the current network and records the result in `.env` as `BASECAMP_TLS`. Safe for an assistant to run itself (no interactive prompts). |
| 3 | `uv run --no-sync python -c "import basecamp_setup; basecamp_setup.connect()"` | Non-interactive connection verification. Prints a line starting with ✓ on success; otherwise relays a fix hint. |
| 4 | `uv run python <script>.py` (from an exercise folder) | Runs an exercise script against `.venv` without re-syncing. |

Runtime pin (manifest-enforced on every install): **Python `>=3.10`** (from `pyproject.toml`). README prose may suggest a specific version; the manifest is the source of truth.

Key dependencies installed into `.venv`:

| Package | Pinned floor | Role |
| ------- | ------------ | ---- |
| `anthropic[bedrock]` | 1.6 | Anthropic SDK with Bedrock support |
| `python-dotenv` | 1.0 | Loads `.env` on every `connect()` |
| `truststore` | 0.10 | Bridges the OS certificate store into Python's HTTPS stack for corporate TLS |
| `matplotlib` | 3.7 | Exercise output rendering |
| `tabulate` | 0.9 | Exercise output rendering |
| `ipykernel` | 6 | Jupyter kernel registration |
| `notebook` | — | Jupyter runtime |

Emulator ports: none — no infrastructure services are declared for this repository. See [[conventions/project]] for the project-wide rules that govern this workflow (never hand-roll installs, never mutate global TLS environment variables, always run `connect()` as the first cell).

## Automation & CI

Primary automation interface: a single Python bootstrap script, `basecamp_setup.py`, at the repo root. There is no Makefile, Justfile, Taskfile, npm script catalog, or equivalent runner. All setup, TLS probing, kernel registration, and connection verification go through sub-commands and flags of this one script.

Documented command catalog (from `README.md § Setup & troubleshooting`):

| Tier | Command | Source |
| ---- | ------- | ------ |
| readme | `python3 basecamp_setup.py` (macOS / Linux) | `README.md#setup-troubleshooting` |

Source control: GitHub. CI provider and workflows: (not determined by analysis).

## Coupling hotspots

- `basecamp_setup` (module, score 1)
