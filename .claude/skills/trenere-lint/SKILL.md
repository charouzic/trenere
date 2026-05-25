---
name: trenere-lint
description: Health-check the coaching wiki for contradictions, stale claims, missing links, and update candidates.
---

# trenere-lint

## Athlete Data Root

Before reading or writing private coaching data, resolve `{athlete-data-root}` using `AGENTS.md`: `TRENERE_ATHLETE_DATA`, then `.trenere-athlete-data`, then `../trenere-athlete-data`. Public core files are read from this repository; athlete-specific profile, logs, workouts, imports, blocks, insights, and sync notes are read or written under `{athlete-data-root}`. Commit public core changes in this repo and private athlete-data changes from `{athlete-data-root}`.


## When To Use

Use this skill when the wiki may be stale, before important planning, after many
imports or reviews, or when the athlete asks whether the coaching memory is
internally consistent. It can also surface candidate learnings that should be
promoted into private athlete memory or public agent instructions, but
`trenere-ask` owns the explicit self-improvement flow.

## Inputs Required

No external input is required. Optional input can include a focus area such as
zones, injuries, recent plans, insights, or index hygiene.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `{athlete-data-root}/wiki/log.md`
- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/injury-history.md`
- `{athlete-data-root}/wiki/profile/preferences.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`
- `wiki/workouts/README.md`
- recent `{athlete-data-root}/wiki/workouts/YYYY-MM.md` files
- `{athlete-data-root}/wiki/insights/README.md`
- insight pages
- `{athlete-data-root}/wiki/blocks/README.md`
- block pages
- `{athlete-data-root}/wiki/meta/last-sync.md`

## Files To Update

- `{athlete-data-root}/wiki/log.md`
- `wiki/index.md` for missing or stale navigation entries
- `{athlete-data-root}/wiki/profile/coaching-directives.md` when repeated patterns should become
  standing directives
- affected wiki pages when fixing clear inconsistencies

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Scan the wiki for contradictions, stale claims, missing index entries, orphan
   insights, outdated zones, and injury notes not reflected in directives.
3. Check whether repeated patterns in logs, reviews, workouts, or insights
   should be promoted to `coaching-directives.md`.
4. Verify `wiki/index.md` links important pages and current status accurately.
5. Verify insight pages are durable and linked or discoverable.
6. Identify whether any finding is a self-improvement candidate:
   athlete-specific learnings belong under `{athlete-data-root}`; reusable agent
   workflow improvements belong in the public core.
7. Make small, obvious fixes when safe.
8. Do not invent missing athlete facts.
9. Prioritize findings by coaching impact, not by how easy they are to describe.
10. Keep the final report concise: no more than 5 findings unless there is a
   safety issue.
11. Append a compact `lint` entry to `{athlete-data-root}/wiki/log.md` with findings and fixes.
12. Show changed files before committing if asked to commit.

## Output Format

Return a short maintenance report, not a wall of text.

Use this exact shape:

```md
Lint result: clean / needs attention / safety concern

Scope: 1 sentence.

Findings:
- P0/P1/P2 — [file] — concise issue and why it matters.

Fixes applied:
- [file] — concise change.

Unresolved:
- Question or missing fact to ask later.

Next:
- One suggested next skill or action.
```

Rules:

- Use `P0` only for safety or serious coaching-risk issues.
- Use `P1` for contradictions, stale injury guidance, missing current-status
  navigation, or bad planning directives.
- Use `P2` for cleanup, missing links, stale wording, and minor hygiene.
- Limit to 5 findings total. If more exist, report the 5 highest-impact findings
  and say how many lower-priority items were omitted.
- Omit empty sections except `Scope` and `Next`.
- If no issues are found, say `Lint result: clean` and include one sentence on
  residual risk, such as sparse data or no recent workouts.
- Do not paste long excerpts from wiki files. Reference paths and summarize.
- Keep `{athlete-data-root}/wiki/log.md` shorter than the final response: 2-5 bullets maximum.

## Edge Cases

- If a contradiction cannot be resolved from the wiki, log it as unresolved and
  ask the athlete next time.
- If zones appear outdated but no newer data exists, mark them as stale rather
  than replacing them.
- If injury notes suggest ongoing risk, ensure `coaching-directives.md` reflects
  conservative planning.
- If an insight is not durable, move the idea into `{athlete-data-root}/wiki/log.md` or mark it for
  review instead of deleting without user approval.
- If private data appears in raw imports, do not commit it.
- If lint finds a reusable agent-workflow improvement, report it as a public
  self-improvement candidate unless the athlete explicitly asked to apply and
  push self-improvement changes.

## Git/Log/Index Update Rules

- Append to `{athlete-data-root}/wiki/log.md` using:

```md
## [YYYY-MM-DD] lint | Wiki health check

Result: clean / needs attention / safety concern.

- Fix: short note, if any.
- Finding: short note, if any.
- Next: short note.
```

- Update `wiki/index.md` for navigation issues.
- Commit only after meaningful updates.
- Default commit:

```bash
git add .
git commit -m "trenere-lint: health-check wiki YYYY-MM-DD"
```
