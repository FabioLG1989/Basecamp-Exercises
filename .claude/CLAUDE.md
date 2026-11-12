# Anthropic Basecamp Exercises

## Tech Stack

- **Python** >=3.10 · **uv** (package manager) · **Jupyter / ipykernel 6**
- **anthropic[bedrock]** 1.6 — Anthropic SDK with Bedrock support
- **python-dotenv** 1.0 — loads `.env` on every `connect()` call
- **truststore** 0.10 — system certificate store for corporate TLS
- **matplotlib** 3.7, **tabulate** 0.9 — exercise output rendering
- **notebook** — Jupyter runtime

## File Placement Guide

- **Basecamp-Exercises** — exercises live under `day{N}/{NN_exercise-name}/` as `.ipynb` or `.py` next to their `README.md` — full table: `docs/llm-wiki/wiki/services/Basecamp-Exercises.md` (exercise folders may also carry a local `CLAUDE.md` that overrides repo-level agent notes inside that folder)

## Essential Commands

| Command | Description | Source |
| ------- | ----------- | ------ |
| `python3 basecamp_setup.py        # macOS / Linux` | From README.md § Setup & troubleshooting | `README.md#setup-troubleshooting` |

## Services & Ports

| Service | Type | Port | Role |
| ------- | ---- | ---- | ---- |
| Basecamp-Exercises | library | — (library — no runtime) | Training exercise notebooks and scripts for Partner Basecamp using the Anthropic SDK |

## Project-Wide Rules

- Never run `basecamp_setup.py` in the agent's own shell when credentials are missing — open a new OS terminal window (see AGENTS.md for the per-OS command) so the user can type the key interactively.
- Never ask the user to paste an API key into chat, and never print, quote, or commit any value from `.env`. Checking that a variable name is present is fine.
- Never disable TLS verification or mutate global cert environment variables (`SSL_CERT_FILE`, `REQUESTS_CA_BUNDLE`, shell profiles, Windows env vars). Use `basecamp_setup.py --check-tls` instead; it scopes the fix to the exercises via `BASECAMP_TLS` in `.env`.
- Notebooks use the **Anthropic Basecamp (.venv)** kernel; the first code cell always calls `connect()` from `basecamp_setup.py`.
- Do not hand-roll installs (`pip install`, custom venvs, manual `.env` writes) — `basecamp_setup.py` is the single source of truth for environment state.
- A per-folder `CLAUDE.md` inside an exercise directory takes precedence over repo-level agent notes within that folder.

## Project Conventions

Before writing or reviewing code in a service, read that service's prescriptive
rules — do not infer them from surrounding files alone:

- `docs/llm-wiki/wiki/services/{id}.md` → `## Conventions & How-To` — that service's coding, multi-file-workflow, and testing rules, with grounded examples.
- `docs/llm-wiki/wiki/conventions/project.md` → `## Project-Wide Rules` — rules that apply across the whole repository.

<!-- LLM_WIKI_START -->
## LLM Wiki
- Router (entry point): `docs/llm-wiki/CLAUDE.md` — decision table and tier discipline. **Read this first.**
- Index (summary catalog): `docs/llm-wiki/wiki/index.md` — one line per page; pick the 1–3 pages whose summaries match your question.
- Before broad code changes: load the router → match the index → read only the matched pages. Stop wikilink traversal at depth 2. Fall back to targeted Grep/Read on the source only if the wiki does not answer.
<!-- LLM_WIKI_END -->
