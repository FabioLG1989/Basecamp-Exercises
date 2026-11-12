---
document_type: index
content_type: descriptive
summary: >-
  Summary catalog for the Basecamp-Exercises LLM wiki — one line per page,
  frontmatter inline.
last_updated: '2026-11-12T19:54:21.420Z'
related:
  - ARCHITECTURE.md
  - SERVICES.md
---
# Basecamp-Exercises LLM Wiki

Summary catalog of every page in this wiki. Each line carries the page summary, document type, tags, and related pages — frontmatter inline so a single read of `index.md` serves Tier 1 retrieval.

## Getting started

- [getting-started](getting-started.md) — *getting-started* — How to run, set up, build, and test this project locally — README setup reproduced verbatim plus wrapper / package-manager / CI command tables, each with its source. **Tags:** getting-started, setup, commands.

## Architecture

- [ARCHITECTURE](ARCHITECTURE.md) — *architecture* — The repository is a single-service, polyrepo-style Python project — not a monorepo. No workspace tool (uv workspaces, Poetry workspaces, Nx, Turborepo, etc.)... **Tags:** architecture, topology, python, jupyter-/-ipykernel.

## Conventions

- [conventions/project](conventions/project.md) — *conventions, prescriptive* — The single home for project-wide conventions — shared dependencies, tooling, quality gates, and service boundaries that recur across services, plus genuinely repo-wide rules; the per-service pages reference it. **Tags:** conventions, project.

## Services catalog

- [SERVICES](SERVICES.md) — *services* — Catalog of services detected in this project with links to service docs. **Tags:** services, catalog. **Related:** [[ARCHITECTURE]].

## Per-service docs

- [Basecamp-Exercises](services/Basecamp-Exercises.md) — *service* — Basecamp-Exercises is a library-shaped training repository that packages the Jupyter notebooks and Python scripts participants work through during Partner Ba... **Tags:** service, python, library, jupyter-/-ipykernel.

## How agents should use this

- Start with this index. Read the 1–3 page bodies whose summaries match your question.
- Follow `**Related:**` `[[wikilinks]]` only when the matched pages reference them.
- Stop wikilink traversal at depth 2.
- If the wiki does not answer your question, run targeted Grep/Read on the source — never re-read the wiki cover-to-cover.
