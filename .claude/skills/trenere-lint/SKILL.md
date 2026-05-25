---
name: trenere-lint
description: Health-check the coaching wiki for contradictions, stale claims, missing links, and update candidates.
---

# trenere-lint

## When To Use

Use before important planning, after many imports/reviews, or when the athlete
asks whether the coaching memory is stale, inconsistent, or messy.

## Inputs Required

None. Optional focus: zones, injuries, plans, insights, index, or public core.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `wiki/coach-knowledge.md`
- `{athlete-data-root}/wiki/log.md`
- `{athlete-data-root}/wiki/profile/*.md`
- recent `{athlete-data-root}/wiki/workouts/YYYY-MM.md`
- `{athlete-data-root}/wiki/insights/`
- `{athlete-data-root}/wiki/blocks/`
- `{athlete-data-root}/wiki/meta/last-sync.md`

## Files To Update

- `{athlete-data-root}/wiki/log.md`
- private profile/directive/insight pages for clear fixes
- public core files only when linting public core and the fix is general

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Look for contradictions, stale claims, missing index entries, orphan insights,
   outdated zones, injury notes not reflected in directives, and patterns that
   should become directives.
3. Make small obvious fixes when safe.
4. Do not invent missing athlete facts.
5. Report at most 5 findings, prioritized by coaching impact.
6. Append a compact `lint` entry to the private log.

## Output Format

```md
Lint result: clean / needs attention / safety concern

Scope: ...

Findings:
- P0/P1/P2 — file — issue and why it matters.

Fixes applied:
- file — change.

Next:
- ...
```

Omit empty sections. P0 is safety only.

## Edge Cases

- If a contradiction cannot be resolved, log it as unresolved.
- If private data appears in public core, stop and report before committing.
- If reusable workflow improvements are found, report them as public
  self-improvement candidates unless asked to apply them.

## Git/Log/Index Update Rules

Log format:

```md
## [YYYY-MM-DD] lint | Wiki health check

Result: clean / needs attention / safety concern.
```

Commit only meaningful updates.
