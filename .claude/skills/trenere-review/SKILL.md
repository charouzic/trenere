---
name: trenere-review
description: Review recent training and decide what should influence future planning.
---

# trenere-review

## Athlete Data Root

Before reading or writing private coaching data, resolve `{athlete-data-root}` using `AGENTS.md`: `TRENERE_ATHLETE_DATA`, then `.trenere-athlete-data`, then `../trenere-athlete-data`. Public core files are read from this repository; athlete-specific profile, logs, workouts, imports, blocks, insights, and sync notes are read or written under `{athlete-data-root}`. Commit public core changes in this repo and private athlete-data changes from `{athlete-data-root}`.


## When To Use

Use this skill after workouts have been imported, before planning a new week, or
when the athlete asks what recent training means.

## Inputs Required

- review window, if specified
- recent workouts in `{athlete-data-root}/wiki/workouts/`
- athlete context from profile files
- subjective context from the athlete, if provided

If no review window is specified, review the most recent useful period available,
usually the last 7-14 days.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/injury-history.md`
- `{athlete-data-root}/wiki/profile/preferences.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`
- recent `{athlete-data-root}/wiki/workouts/YYYY-MM.md` files
- `{athlete-data-root}/wiki/insights/README.md`
- recent insight pages if relevant
- `{athlete-data-root}/wiki/log.md`

## Files To Update

- `{athlete-data-root}/wiki/log.md`
- `{athlete-data-root}/wiki/insights/*.md` only for durable, athlete-specific conclusions
- `wiki/index.md` if current status changes
- `{athlete-data-root}/wiki/profile/coaching-directives.md` only when a durable pattern should guide
  future planning

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Identify the review window and relevant workout files.
3. Summarize what happened: frequency, volume, long sessions, hard sessions,
   missed sessions, and subjective notes.
4. Estimate easy/hard balance from workout type, tags, RPE, HR, pace, or notes.
5. State what went well.
6. State what looks risky, including load spikes, too much intensity, poor
   recovery, soreness, illness, or injury flags.
7. Explain what should affect next week.
8. Decide whether any durable insight belongs in `{athlete-data-root}/wiki/insights/`.
9. If creating an insight, include conclusion, evidence, planning implication,
   and date.
10. Append a `review` entry to `{athlete-data-root}/wiki/log.md`.
11. Show changed files before committing if asked to commit.

## Output Format

Return:

- review window
- what happened
- easy/hard balance
- what went well
- what looks risky
- what should affect next week
- durable insight filed or reason none was filed
- recommended next skill, usually `/trenere-plan`

## Edge Cases

- If recent workout data is sparse, say so and avoid overconfident conclusions.
- If subjective notes conflict with metrics, call out the mismatch.
- If there are safety-boundary symptoms, advise stopping training and seeking
  medical evaluation.
- If an idea is only about one ordinary session, log it but do not create an
  insight page.
- If the athlete skipped sessions, treat that as planning data, not a moral
  failure.

## Git/Log/Index Update Rules

- Append to `{athlete-data-root}/wiki/log.md` using:

```md
## [YYYY-MM-DD] review | Recent training reviewed

Short notes.
```

- Create `{athlete-data-root}/wiki/insights/YYYY-MM-DD-short-title.md` only for durable insights.
- Update `wiki/index.md` if a new insight or major status change should be
  discoverable.
- Commit only after meaningful updates.
- Default commit:

```bash
git add .
git commit -m "trenere-review: review recent training YYYY-MM-DD"
```
