---
document_type: conventions
content_type: prescriptive
summary: >-
  The single home for project-wide conventions — shared dependencies, tooling,
  quality gates, and service boundaries that recur across services, plus
  genuinely repo-wide rules; the per-service pages reference it.
last_updated: '2026-11-12T19:54:21.420Z'
tags:
  - conventions
  - project
---
# Project-Wide Conventions

This page is the single home for the conventions and rules that apply across this project — shared topics that recur across services and genuinely repo-wide rules. See the linked per-service pages for service-specific deltas.

## Project-Wide Rules

- Never run `basecamp_setup.py` in the agent's own shell when credentials are missing — open a new OS terminal window (see AGENTS.md for the per-OS command) so the user can type the key interactively.
- Never ask the user to paste an API key into chat, and never print, quote, or commit any value from `.env`. Checking that a variable name is present is fine.
- Never disable TLS verification or mutate global cert environment variables (`SSL_CERT_FILE`, `REQUESTS_CA_BUNDLE`, shell profiles, Windows env vars). Use `basecamp_setup.py --check-tls` instead; it scopes the fix to the exercises via `BASECAMP_TLS` in `.env`.
- Notebooks use the **Anthropic Basecamp (.venv)** kernel; the first code cell always calls `connect()` from `basecamp_setup.py`.
- Do not hand-roll installs (`pip install`, custom venvs, manual `.env` writes) — `basecamp_setup.py` is the single source of truth for environment state.
- A per-folder `CLAUDE.md` inside an exercise directory takes precedence over repo-level agent notes within that folder.
