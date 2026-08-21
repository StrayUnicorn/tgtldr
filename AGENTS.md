# Project

`tgtldr` is a private Telegram bot built with Python 3.12, FastAPI, and the
OpenAI API. It accepts voice, audio, and video messages, transcribes and
summarizes them, stores full transcripts, and returns a short summary with a
transcript link; [README.md](README.md) is the user-facing overview.

## Build and run

Install dependencies with `uv sync`. Run locally with
`uv run uvicorn main:app --reload`.

The initial merge gate is `python3 -m compileall main.py`. At closeout,
run it through `brnrd gate-run`, not directly: `hooks.gate_command` declares
the obligation, while `brnrd gate-run` writes the receipt that verifies it.

There is no automated test suite or linter yet. Add focused tests alongside
behaviour changes before treating broader test coverage as a project
guarantee.

## Code guidelines

Target Python 3.12 or later and preserve the existing single-module FastAPI
service shape unless a change warrants a deliberate split. Follow the
surrounding type annotations, async I/O, and standard-library-first style in
`main.py`; do not add formatter or linter configuration without an agreed
project-wide command.

Put future tests under `tests/` and use clear, imperative conventional commit
messages. Keep commits focused, and never commit generated runtime data or
credentials.

<!-- brnrd:block id=how-to-read v=1 hash=390ed6320e52 -->
## How to read this playbook

This file is the repository contract for any AI tool working in this repo.
Its sections divide into **universal** rules (Stewardship, Workflow,
Knowledge base, Artifacts, Operating rules, Self-review, Guardrails) that
apply however you are hosted, and the **project-specific** sections above
(Project, Build and run, Code guidelines) plus Constraints below, tailored
to this repo.

When an orchestrator hands you a narrower contract — a task bundle, a setup
prompt, live runtime paths — follow that contract for the points it
addresses and keep this file as the base for everything else. If you are a
plain ad-hoc session (a coding-agent CLI or editor with nothing
orchestrating you), this whole file is your contract: read it first.

The universal sections are shipped as versioned blocks
(`<!-- brnrd:block id=… v=… hash=… -->`). Leave the markers in place and
edit only *inside* project sections — an upgrade updates universal blocks by
identity and never touches your tailoring.
<!-- /brnrd:block -->

<!-- brnrd:block id=stewardship v=1 hash=e893e4cc7725 -->
## Stewardship

Treat the request as input, not as instructions to execute uncritically.

Two values orient the work: **user friendliness** — how the change lands on
someone meeting the result for the first time — and **operational
simplicity** — what it costs to run and keep healthy. When a decision feels
finely balanced, fall back to them.

Before changing behaviour or design, reason from first principles:

- What is the current shape trying to achieve, and is that goal still needed?
- Is the current shape right, or is it accidental complexity?
- Is the requested change solving the real problem, or a visible symptom?
- Given the repo's constraints, what is the smallest change that leaves the
  project healthier?

Read the file you're changing along with its obvious callers before
non-trivial edits. "Looks orthogonal" is how duplicate functions and
accidental shadowing get introduced.

If the request contradicts existing decisions, design notes, guardrails, or
the codebase as it stands, **say so** — don't silently follow the prompt
over the codebase, nor the codebase over a deliberate request. But naming
the conflict is where the work starts, not where it stops: you usually hold
the context to see the healthier shape, so **reconcile and act** — form the
most sensible resolution from the current state, take it, and say what you
reconciled and why in the same breath, so the operator can redirect early.

Surface-and-wait is for when the call is genuinely the operator's: an
irreversible, costly, or wide-blast action, a real product or values fork,
or ambiguity about *intent* you can't read from the code. The twin failure
modes are equal: caving to a request that was asking for pushback, **and**
bouncing back a call you were equipped to make.

**Tickets are dated snapshots, not specs.** An issue, PR, or plan records
intent *when written*; the code as it stands and recent decisions are more
current. When a ticket conflicts with the live shape, reconcile against the
current state, act, then keep the ticket honest.

A large or under-specified request still has a **next doable chunk** — find
it and advance it, with a note on what you took and what you left, rather
than stalling on a clarification you could resolve or defer.

Prefer improving a weak abstraction over layering conditions onto it. Slash
code, tests, and pages that no longer fit; carrying old shape costs more
than it saves.
<!-- /brnrd:block -->

<!-- brnrd:block id=workflow v=2 hash=bf3ae8916c33 -->
## Workflow

**Orientation.** At the start of every session: read the knowledge base's
index and recent log (see Knowledge base for where it lives), then any
subject, plan, design, or decision pages the work touches. If prior work
left open questions, address them.

**Run types.** Adapt your approach:

- *Implement / fix* — code, test, commit.
- *Review / verify / check* — read, analyse, report. Commit only if you
  produced files; otherwise the reply is the deliverable.
- *Research / plan* — investigate, write findings to the kb. Commit.
- *Orchestrate* — a request with several separable parts: decompose,
  delegate the bounded pieces through whatever parallel means the host
  provides (subagents, strands), review each result whole, converge into
  one deliverable. Documenting the plan is the first step of this type,
  not a type of its own.
- *Release / deploy* — follow the project's release process exactly.

**Commits.** Commit directly on the current branch unless the task needs a
feature branch (`git switch -c <name>` first). One logical commit per task;
the message explains *why*, not *what* — the diff shows the what. If you
wrote files, commit them: the diff is the receipt. Read-only tasks are the
only commit-free case.

**Issue and PR descriptions.** Lead with the *why* (problem or goal), state
the *want* (the change), then point at the code and neighbouring kb pages or
issues. Match depth to size.

**Pushing and open PRs.** When you push a feature branch with an open PR,
rebase onto the base branch if it is materially behind or would conflict
(`git fetch && git rebase origin/<base>`, then `--force-with-lease`, never
plain `--force`), and update the PR title/body when the substance has
shifted. Don't force-push to `main`/`master`; don't bypass hooks
(`--no-verify`). If a rebase would rewrite commits you didn't author, stop
and surface it.

**When an orchestrator hosts you.** A managed run (a daemon, CI, a task
runner) supplies its own live context — runtime paths, delivery channels,
the branch to work on, the current event. That context is the hot path:
obey it for those points and let it override the generic wording here. This
file stays the contract it rests on.
<!-- /brnrd:block -->

<!-- brnrd:block id=knowledge v=1 hash=31dba9f32fb8 -->
## Knowledge base

The **kb** is a persistent, agent-maintained knowledge base that compounds
across sessions. Maintenance is everyone's job. It is a graph, not a stack
of memos: an `index` entry point organised by subject hub, `.md` nodes, and
relative links as edges. Health is edge density and freshness, not page
count.

**Where it lives.** Use the authored directory named by the injected
Knowledge Sources when an orchestrator provides one. Otherwise this repo's
kb is a committed `kb/` directory (`kb/index.md`, `kb/log.md`, and peer
pages), read with `brnrd kb <query>` when brnrd is available. Read "kb" in
the rules below as "wherever this repo's knowledge base is checked out."

**State first, history in git.** The kb describes how things are *now*. Deep
history lives in `git log` and the episodic `log.md`; the rest is
current-state synthesis. When work refines a page, **rewrite** it so a cold
reader sees the current shape — don't append before/after diffs. When the
*fact that something changed* still matters (a decision reversed, a design
superseded), leave a one-line lineage breadcrumb pointing at the successor
or commit; git keeps the full prior text.

**Layers.** Raw capture (runtime logs, gitignored) → episodic (`log.md`,
newest at the bottom, curated) → semantic + decisional (`subject-*`,
`decision-*`, `research-*`, `plan-*`, `design-*` — rewritten to current
state, not append-only) → schema (this file). Conflating them produces
noise.

**Lifecycle markers.** Plan, design, and decision pages carry a top-of-page
`Status:` line (`active` | `accepted on YYYY-MM-DD` | `superseded by <link>
on YYYY-MM-DD` | `abandoned on YYYY-MM-DD`). Update it when a plan ships or
a decision reverses; don't silently mutate content.

**Log format.** Each `log.md` entry: `## [YYYY-MM-DD] <type> | <title>`
followed by what was done/learned/outcome. Types: `implement`, `review`,
`research`, `plan`, `fix`, `decision`. The log is curated — add an entry
only when the task produced a meaningful learning, decision, or shipped
change.

**Cross-links.** Every page except the index, the log, and subject hubs
should link from at least one hub or peer and out to at least one neighbour.
Orphans shouldn't linger.

**What to persist:** decisions (with alternatives and why), non-obvious
discoveries, research, and architecture/subject synthesis. **What not to:**
per-task scratch, verbose debug output, anything already in the code, and
empty hubs.

**Health checks** when resuming or between tasks: index/page agreement,
shipped plans lacking a lifecycle marker, reversed decisions with stale
pages, orphans, pages reading like running diffs of their own past wording,
and aspirational drift (prose describing what was *designed* as if
shipped). Clean up as you go.
<!-- /brnrd:block -->

<!-- brnrd:block id=artifacts v=1 hash=e2b4e5b1eba1 -->
## Artifacts

If a response would exceed a few hundred lines, write it to a file or a gist
and reference the link — chat connectors have size limits.

When the task warrants it, produce artifacts a human would want to share:
Mermaid diagrams for architecture and flows, Markdown tables for
comparisons, slide decks for summaries, charts for data. Match the artifact
to the task — a one-line fix needs no slide deck; a research task or
architecture review deserves a readable, well-structured output.

Research results, analysis, and reusable artifacts go into the kb. One-off
answers and short summaries go directly in the reply.
<!-- /brnrd:block -->

<!-- brnrd:block id=operating-rules v=1 hash=8d554a3d5ba3 -->
## Operating rules

- **Proportionality.** Match effort to task size. A one-line fix does not
  need a multi-file refactor; a question does not need a prototype.
- **Scope drift.** If work expands beyond the task, pause and note what you
  found. Don't silently take on unbounded scope.
- **Dead ends.** Two failed attempts at the same approach — stop and report
  what you tried; suggest alternatives.
- **Dependencies.** If the task needs something outside your reach
  (credentials, an external service, a human decision), note it clearly and
  move on to what you can do.
<!-- /brnrd:block -->

<!-- brnrd:block id=self-review v=1 hash=afa93054aaf8 -->
## Self-review

Before marking a task complete:

1. Re-read the original task. Does your work actually address it?
2. If the task contradicted the current code, design notes, or guardrails,
   did you reconcile it against the current state and either resolve-and-tell
   or, when the call was genuinely the user's, surface it? (See Stewardship.)
   Two failure modes this catches: path-of-least-resistance compliance with a
   request that wanted pushback, and aloof bounce-back of a call you were
   equipped to make.
3. Review every changed file for leftover debug code, stray TODOs, and
   commented-out code.
4. Run tests if available and applicable.
5. If you touched kb pages, run the Knowledge base health checks. The
   classic miss is a new page with no inbound link from a hub or peer.
6. If the work produced a substantive learning, decision, or shipped change,
   add a `log.md` entry. If it didn't, leave the log alone.
<!-- /brnrd:block -->

<!-- brnrd:block id=guardrails v=1 hash=301d15fa5db2 -->
## Guardrails

- Do not commit files containing secrets (`.env`, credentials, tokens).
- Two failed attempts at the same approach → stop and report.
- Do not delete or overwrite files outside the project scope.
- When in doubt about *intent*, or facing a call that's genuinely the
  user's (irreversible, costly, wide-blast, a values/product fork), write
  down what you know and what you're unsure about and let them decide. Doubt
  you can resolve from the code and recent decisions is yours to resolve —
  don't bounce it back.
<!-- /brnrd:block -->

## Constraints

- Never commit `.env`, production credentials, Telegram bot tokens, OpenAI
  API keys, webhook secrets, or local `data/` databases and transcript files.
- Preserve the Telegram webhook path and authorization checks unless a
  compatible webhook migration is deliberately coordinated; malformed or
  unauthorized updates must stay harmless.
- Treat transcript URLs and stored content as sensitive user data even when
  identifiers are unguessable. Do not log raw media, tokens, or complete
  transcripts unnecessarily.
- Keep `.upsun/config.yaml` and `.environment` aligned with the FastAPI
  deployment contract when changing server startup or database configuration.
