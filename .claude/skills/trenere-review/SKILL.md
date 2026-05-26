---
name: trenere-review
description: Review recent training and decide what should influence future planning.
---

# trenere-review

## When To Use

Use after workouts are imported, before planning, or when the athlete asks what
recent training means.

## Agent Decision Rules

- Review recent training to decide what should change future planning, not to
  praise or grade the athlete.
- Separate observed facts, conservative interpretations, and action
  implications.
- Treat skipped sessions, extra unplanned volume, unusually hard easy days,
  sleep/stress changes, and niggles as planning inputs.
- Do not file durable insights unless the pattern is likely to matter in a later
  plan.
- If data is sparse or device metrics conflict with subjective notes, state the
  uncertainty and avoid strong conclusions.
- If planned-vs-actual review needs planned blocks or target ranges, route
  through `trenere-coros-fetch` and check for `COROS_ACCESS_TOKEN`.

## Inputs Required

- review window, if specified
- recent workouts
- athlete context
- subjective context, if provided

Default window: most recent useful 7-14 days.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `wiki/coach-knowledge.md`
- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/injury-history.md`
- `{athlete-data-root}/wiki/profile/preferences.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`
- recent `{athlete-data-root}/wiki/workouts/YYYY-MM.md`
- recent `{athlete-data-root}/wiki/log.md`
- relevant `{athlete-data-root}/wiki/insights/*.md`

## Files To Update

- `{athlete-data-root}/wiki/log.md`
- `{athlete-data-root}/wiki/insights/*.md` only for durable athlete-specific
  conclusions
- `{athlete-data-root}/wiki/profile/coaching-directives.md` only for durable
  planning rules

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Identify the review window.
3. Summarize frequency, volume, long sessions, hard sessions, missed sessions,
   and subjective notes.
4. Estimate easy/hard balance.
5. Interpret device data conservatively; do not overfit one workout.
6. State what went well, what looks risky, and what should affect next week.
7. File durable insights only when likely to change future planning.
8. Append a compact `review` entry to the private log.

## Output Format

- review window
- what happened
- easy/hard balance
- went well
- risky
- effect on next week
- insight filed or reason none
- next skill, usually `trenere-plan`

## Edge Cases

- Sparse data: avoid strong conclusions.
- Metrics conflict with subjective notes: call out the mismatch.
- Safety symptoms: advise stopping training and seeking medical evaluation.
- Skipped sessions are planning data, not moral failure.

## Git/Log/Index Update Rules

Log format:

```md
## [YYYY-MM-DD] review | Recent training reviewed

Short notes.
```

Commit only meaningful updates.
