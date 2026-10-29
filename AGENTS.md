# Project

`tgtldr` is a private Telegram transcription and summary bot implemented as a Python 3.12 FastAPI webhook service. It accepts audio and video messages, uses OpenAI for transcription and summarisation, and serves expiring transcript links. Start with [README.md](README.md) for its user-facing behaviour.

## Build and run

Install dependencies with `uv sync`, then run locally with:

```sh
uv run uvicorn main:app --reload
```

There is no automated test suite or linter yet. For every code change, run the declared merge gate through `brnrd gate-run` (not just the underlying command):

```sh
python3 -m compileall main.py
```

When `uv` is available, also run `uv sync` before exercising the application.

## Code guidelines

- Target Python 3.12 and preserve the existing straightforward, typed style.
- Keep application logic in `main.py` until a split has a clear ownership boundary.
- Do not add new dependencies without a concrete runtime need and an update to `pyproject.toml` / `uv.lock`.
- Add tests alongside any non-trivial behavioural change; use `tests/` when the first suite is introduced.
- Use concise imperative commit subjects that explain why the change matters.

## Stewardship

Treat requests as input, not orders to execute uncritically. Prefer the smallest reversible change that improves user friendliness and operational simplicity. Read a file and its callers before non-trivial edits; surface genuine product, security, or irreversible forks rather than guessing.

## Workflow

At the start of a task, read the account-home knowledge index and the relevant subject page. Implementations require code, a proportionate check, and a commit on the current branch. Reviews and investigations are read-only unless producing a durable artifact. For separable work, delegate bounded research and review returned diffs in full before convergence.

## Knowledge base

This repository uses account-home knowledge, not a committed `kb/` directory. Use the directory named in the managed run context; its current entry point is `index.md` and the project hub is `subject-tgtldr.md`. Record durable discoveries, decisions, and shipped changes there, with a curated entry in `log.md`; do not store secrets, transcript contents, or per-task scratch.

## Operating rules

- Match effort to the task; do not turn a small fix into an unrequested redesign.
- After two failed attempts at the same approach, stop and report the evidence.
- Keep scope bounded; name newly discovered work instead of silently absorbing it.
- Do not commit secrets, credentials, tokens, local databases, or real transcript data.

## Constraints

- Treat Telegram authorization, webhook validation, transcript-link secrecy, and retention/cleanup as security-sensitive behaviour.
- Never expose or use real Telegram/OpenAI credentials or user transcript contents without explicit approval.
- Keep `.environment`, local SQLite data, and other runtime-generated files out of commits.
- Preserve Upsun compatibility in `.upsun/config.yaml` and environment-variable handling unless a task explicitly changes deployment behaviour.
