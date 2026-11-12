---
document_type: service
content_type: descriptive
summary: >-
  Basecamp-Exercises is a library-shaped training repository that packages the
  Jupyter notebooks and Python scripts participants work through during Partner
  Ba...
last_updated: '2026-11-12T19:54:21.420Z'
tags:
  - service
  - python
  - library
  - jupyter-/-ipykernel
service_id: Basecamp-Exercises
---
# Service: Basecamp-Exercises

## Purpose

Basecamp-Exercises is a library-shaped training repository that packages the Jupyter notebooks and Python scripts participants work through during Partner Basecamp. It wraps the Anthropic SDK (with Bedrock support) behind a single setup module so that every exercise shares one `.venv`, one `.env`, and one named kernel. The repository has no runtime surface: exercises execute locally in the participant's editor or notebook.

## Overview

The repo root holds `basecamp_setup.py` plus two training-day directories (`day1/`, `day2/`), each containing numbered exercise folders laid out as `day{N}/{NN_exercise-name}/` with a `README.md` and either an `.ipynb` or `.py` alongside. See the Facts and Notable Patterns tables on this page for the authoritative layout; the architectural narrative in [[ARCHITECTURE]] covers the repo-wide shape.

Environment bootstrap is centralized in `basecamp_setup.py`: it installs **uv** if missing, builds `.venv`, syncs dependencies pinned through `pyproject.toml`, registers the `Anthropic Basecamp (.venv)` Jupyter kernel, prompts for the participant's API key, writes it to `.env`, and probes TLS so corporate certificate stores work through **truststore**. The same module exposes `connect()`, which every notebook's first code cell invokes to re-read `.env` and verify the Anthropic client before the exercise runs.

At runtime, exercises talk to Anthropic through the `anthropic[bedrock]` SDK — the only external dependency — and render output with `matplotlib` and `tabulate`. There is no HTTP surface, no database, no auth layer, no event pipeline, and no test suite in this repository; the single external integration is the Anthropic API, described in the deterministic tables above.

Per-folder `CLAUDE.md` files (for example under `day1/04_diagnosing-ai-problems/`) may override repo-level agent notes inside their own exercise directory, so an agent editing within a specific exercise should treat that folder's notes as the local source of truth.

## Facts

| Fact | Value |
| --- | --- |
| Type | library |
| Language | python >=3.10 |
| Path | . |
| Manifest | pyproject.toml |
| Frameworks | Jupyter / ipykernel 6; anthropic[bedrock] 1.6, python-dotenv 1.0, truststore 0.10, matplotlib 3.7, tabulate 0.9 |
| External services | Anthropic |

## File Placement

| Artefact | Location | Example |
| --- | --- | --- |
| exercise notebook | `day{N}/{NN_exercise-name}/{Notebook_Name}.ipynb` | `day1/02_developer-platform/Developer_Platform.ipynb` |
| exercise Python script | `day{N}/{NN_exercise-name}/{Script_Name}.py` | `day1/04_diagnosing-ai-problems/Diagnose_Fix_Brief.py` |
| exercise README | `day{N}/{NN_exercise-name}/README.md` | `day1/03_prompt-rescue/README.md` |
| exercise supporting markdown | `day{N}/{NN_exercise-name}/{document}.md` | `day1/04_diagnosing-ai-problems/diagnostic-framework.md` |
| per-folder agent instructions | `day{N}/{NN_exercise-name}/CLAUDE.md` | `day1/04_diagnosing-ai-problems/CLAUDE.md` |
| repo-root setup script | `basecamp_setup.py` | `basecamp_setup.py` |
| env template | `.env.example` | `.env.example` |

## Configuration

(not determined by analysis)

## Dependencies

| Group | Packages |
| --- | --- |
| production | `Anthropic`, `anthropic`, `ipykernel`, `matplotlib`, `notebook`, `python-dotenv`, `tabulate`, `truststore` |

## Testing

No automated tests yet — candidate for a new test suite.

## Conventions & How-To

### Code Rules

- Place new exercises under `day{N}/{NN_exercise-name}/` as either an `.ipynb` or a `.py` next to a `README.md`, matching the existing numbered-folder pattern.
- In notebooks, keep `connect()` from `basecamp_setup.py` as the first code cell so `.env` is re-read on every run (see [[conventions/project]] for the kernel rule).
- Read `ANTHROPIC_MODEL` (via `basecamp_setup.py`) as the workhorse `MODEL` variable in notebooks; leave the fast (Haiku) and big (Opus) comparison models fixed as named constants.

### Multi-File Workflows

- Treat `basecamp_setup.py` as the single source of truth for environment state — reinstall, kernel registration, `.env` writes, and TLS probing all flow through it (see [[conventions/project]]).
- When editing inside an exercise folder that carries its own `CLAUDE.md`, follow that local file's rules over the repo-level notes for changes within that folder (see [[conventions/project]]).

### Observability & Ops

- Diagnose certificate failures with `basecamp_setup.py --check-tls`, which records the working mode in `.env` as `BASECAMP_TLS`; never mutate global cert environment variables (see [[conventions/project]]).
- Run standalone exercise scripts with `uv run python <script>.py` from the exercise folder so they resolve against the shared `.venv`.

### Boundaries & Dependencies

- The only external integration is the Anthropic API through the `anthropic[bedrock]` SDK; do not introduce additional network dependencies for exercises.
- Pin new runtime dependencies in `pyproject.toml` and let `basecamp_setup.py` sync them — do not hand-roll `pip install` or alternate virtualenvs (see [[conventions/project]]).

Project-wide rules live in [[conventions/project]].
