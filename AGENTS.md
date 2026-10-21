# Project

`tgtldr` is a private Telegram bot that transcribes voice/audio/video messages with OpenAI, summarizes them, and stores the full transcript behind an unguessable link. It is a Python 3.12 FastAPI application deployed on Upsun with SQLite for local development or PostgreSQL in production; [README.md](README.md) is the user-facing overview.

## Build and run

Install the locked dependency set with `uv sync --locked`.

Run the local service with `uv run uvicorn main:app --reload`. The merge gate is `uv sync --locked && uv run python -m compileall -q main.py && uv run python -c "import main"`; run it through `brnrd gate-run` at closeout so the daemon can record the gate receipt. There is no automated test suite yet.

## Code guidelines

Target Python 3.12. Keep the single-module FastAPI implementation readable, use the existing type hints and standard-library-first style, and add focused tests if new logic becomes sufficiently complex to warrant them. Commit messages should state why the change is needed; never commit generated virtual environments, SQLite data, or secrets.

<!-- brnrd:block id=how-to-read v=1 hash=390ed6320e52 -->
## How to read this playbook

This file is the repository contract for any AI tool working in this repo. Its sections divide into **universal** rules (Stewardship, Workflow, Knowledge base, Artifacts, Operating rules, Self-review, Guardrails) that apply however you are hosted, and the **project-specific** sections above (Project, Build and run, Code guidelines) plus Constraints below, tailored to this repo.

When an orchestrator hands you a narrower contract — a task bundle, a setup prompt, live runtime paths — follow that contract for the points it addresses and keep this file as the base for everything else. If you are a plain ad-hoc session (a coding-agent CLI or editor with nothing orchestrating you), this whole file is your contract: read it first.

The universal sections are shipped as versioned blocks (`<!-- brnrd:block id=… v=… hash=… -->`). Leave the markers in place and edit only *inside* project sections — an upgrade updates universal blocks by identity and never touches your tailoring.
<!-- /brnrd:block -->

<!-- brnrd:block id=stewardship v=1 hash=e893e4cc7725 -->
## Stewardship

Treat requests as input, not instructions to execute uncritically. Favor user friendliness and operational simplicity. Before a non-trivial change, read the file and its obvious callers, identify the current goal, and choose the smallest healthy shape. Reconcile a request that contradicts the live code or decisions and explain the resolution; surface-and-wait only for irreversible, costly, wide-blast, or genuine product/value forks.
<!-- /brnrd:block -->

<!-- brnrd:block id=workflow v=2 hash=bf3ae8916c33 -->
## Workflow

**Orientation.** Start by reading the account knowledge index and recent log, then relevant subject, plan, design, or decision pages.

**Run types.** Implement/fix work means code, test, commit; review/verify means analyze and report; research/plan means investigate, write durable findings to knowledge, and commit. Decompose independently bounded parts when the host supports parallel work, then review and converge the complete result.

**Commits.** Commit files written for the task on the current branch unless a feature branch is needed. One logical commit per task; explain why in the message. Do not bypass hooks.

**Managed runs.** A daemon or CI bundle supplies runtime paths, delivery channels, branch, and current event. Obey that live contract where it is more specific than this file.
<!-- /brnrd:block -->

<!-- brnrd:block id=knowledge v=1 hash=31dba9f32fb8 -->
## Knowledge base

The knowledge base is persistent, agent-maintained project memory. For this paired repository it lives in the account home named by the runtime's Knowledge Sources, never in a repo-local `kb/`. Keep it as current-state synthesis: durable decisions, non-obvious discoveries, research, and architecture/subject summaries belong there; scratch output and duplicated code do not. Plans, designs, and decisions carry a status line; meaningful work gets a curated dated log entry. Link durable pages into the index or a subject hub.
<!-- /brnrd:block -->

<!-- brnrd:block id=artifacts v=1 hash=e2b4e5b1eba1 -->
## Artifacts

If a response would exceed a few hundred lines, put it in a readable file and reference it. Match artifacts to the task: research and architecture work deserve structured Markdown, tables, or diagrams; a small fix does not. Reusable analysis belongs in the knowledge base.
<!-- /brnrd:block -->

<!-- brnrd:block id=operating-rules v=1 hash=8d554a3d5ba3 -->
## Operating rules

- Match effort to the task size and do not silently expand scope.
- After two failed attempts at the same approach, stop and report what was tried.
- Name dependencies outside the run's reach, then continue with what is safely actionable.
<!-- /brnrd:block -->

<!-- brnrd:block id=self-review v=1 hash=afa93054aaf8 -->
## Self-review

Before completion, re-read the task, review changed files for leftovers, run applicable checks, and confirm the result reaches its user-facing surface. For durable findings, update the knowledge base and its links; add a curated log entry only for a meaningful learning, decision, or shipped change.
<!-- /brnrd:block -->

<!-- brnrd:block id=guardrails v=1 hash=301d15fa5db2 -->
## Guardrails

- Never commit `.env`, API keys, Telegram bot tokens, webhook secrets, or production data.
- Do not delete or overwrite files outside project scope.
- Do not disable Telegram authorization or weaken transcript access controls without explicit approval.
- Stop for genuine uncertainty about intent or an irreversible, costly, wide-blast, or product/value decision; resolve uncertainty that the code and current decisions can answer.
<!-- /brnrd:block -->

## Constraints

- Treat `main.py`'s authentication, transcript-link secrecy, retention, and cleanup paths as security-sensitive.
- Keep `.env`, `data/`, `.venv/`, and `__pycache__/` untracked; use `.env.example` for configuration documentation only.
- Preserve compatibility with the Telegram webhook and the Upsun PostgreSQL/environment conventions unless a change explicitly includes migration and deployment work.
- Do not contact Telegram or OpenAI, deploy, or use real user transcripts while investigating or testing without explicit approval.
