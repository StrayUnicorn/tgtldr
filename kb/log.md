# Activity Log

Curated chronological narrative. Newest entries at the bottom. Add an
entry when a task produced a meaningful learning, decision, or shipped
change. Format:

```
## [YYYY-MM-DD] <type> | <title>

<what was done, what was learned, outcome>
```

Types: `implement`, `review`, `research`, `plan`, `fix`, `decision`.

## [2026-08-21] decision | Establish repository operating contract

Set up the repository contract and committed knowledge base. The initial
merge gate is `python3 -m compileall main.py`, run through
`brnrd gate-run`; broader test and lint coverage has not yet been chosen.
`uv` was absent on the newly cloned machine, so the dependency-free Python
compiler is the portable initial gate.
