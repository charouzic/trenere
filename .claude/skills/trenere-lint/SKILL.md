---
name: trenere-lint
description: Health-check the coaching wiki for contradictions, stale claims, missing links, and update candidates.
---

# trenere-lint

## When To Use

Use this skill when the wiki may be stale, before important planning, after many
imports or reviews, or when the athlete asks whether the coaching memory is
internally consistent.

## Inputs Required

No external input is required. Optional input can include a focus area such as
zones, injuries, recent plans, insights, or index hygiene.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `wiki/log.md`
- `wiki/profile/athlete.md`
- `wiki/profile/injury-history.md`
- `wiki/profile/preferences.md`
- `wiki/profile/coaching-directives.md`
- `wiki/workouts/README.md`
- recent `wiki/workouts/YYYY-MM.md` files
- `wiki/insights/README.md`
- insight pages
- `wiki/blocks/README.md`
- block pages
- `wiki/meta/last-sync.md`

## Files To Update

- `wiki/log.md`
- `wiki/index.md` for missing or stale navigation entries
- `wiki/profile/coaching-directives.md` when repeated patterns should become
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
6. Make small, obvious fixes when safe.
7. Do not invent missing athlete facts.
8. Append a `lint` entry to `wiki/log.md` with findings and fixes.
9. Show changed files before committing if asked to commit.

## Output Format

Return:

- lint scope
- findings
- fixes applied
- unresolved questions
- suggested follow-up skill, if any

## Edge Cases

- If a contradiction cannot be resolved from the wiki, log it as unresolved and
  ask the athlete next time.
- If zones appear outdated but no newer data exists, mark them as stale rather
  than replacing them.
- If injury notes suggest ongoing risk, ensure `coaching-directives.md` reflects
  conservative planning.
- If an insight is not durable, move the idea into `wiki/log.md` or mark it for
  review instead of deleting without user approval.
- If private data appears in raw imports, do not commit it.

## Git/Log/Index Update Rules

- Append to `wiki/log.md` using:

```md
## [YYYY-MM-DD] lint | Wiki health check

Short notes.
```

- Update `wiki/index.md` for navigation issues.
- Commit only after meaningful updates.
- Default commit:

```bash
git add .
git commit -m "trenere-lint: health-check wiki YYYY-MM-DD"
```
