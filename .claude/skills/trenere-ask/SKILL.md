---
name: trenere-ask
description: General Trenere entrypoint for questions, advice, training-load adjustments, and routing to the right Trenere workflow.
---

# trenere-ask

## When To Use

Use when the athlete is unsure what to do, asks a coaching question, wants a
load adjustment, asks whether Trenere learned anything, or needs routing to
onboard, import, review, plan, lint, or COROS fetch.

## Inputs Required

- athlete question
- only ask 1-3 follow-ups if missing context materially changes the answer

## Files To Read

Always:

- `AGENTS.md`
- `wiki/index.md`
- `wiki/coach-knowledge.md`
- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`

As needed:

- `{athlete-data-root}/wiki/profile/injury-history.md`
- `{athlete-data-root}/wiki/profile/preferences.md`
- recent `{athlete-data-root}/wiki/workouts/YYYY-MM.md`
- `{athlete-data-root}/wiki/log.md`
- relevant `.claude/skills/trenere-*/SKILL.md`

Resolve `{athlete-data-root}` via `AGENTS.md`: `TRENERE_ATHLETE_DATA`, then
`.trenere-athlete-data`, then `../trenere-athlete-data`.

## Files To Update

Usually none. Update only when the answer creates useful durable memory:

- `{athlete-data-root}/wiki/log.md` for decisions or handoffs
- `{athlete-data-root}/wiki/profile/coaching-directives.md` for durable rules
- `{athlete-data-root}/wiki/insights/` for durable athlete-specific conclusions
- public core files only for explicit general self-improvement

If external workout records are fetched or viewed, preserve/import them unless
the athlete explicitly asks for a no-write summary.

## Steps

1. Read required files.
2. Classify the request: answer, adjust, route, update, or learn.
3. Apply safety boundaries from `AGENTS.md`.
4. Use wiki context before asking questions.
5. If context is missing but risk is low, state assumptions and answer
   conservatively.
6. If a specific workflow is needed, name the skill and continue if the athlete
   asked you to proceed.
7. For self-improvement, split public agent learning from private athlete
   learning. Never put private athlete facts in the public repo.

## Routing

- `trenere-onboard`: profile setup or update.
- `trenere-coros-fetch`: get read-only COROS data.
- `trenere-import`: add pasted/staged workouts to the wiki.
- `trenere-review`: interpret recent training.
- `trenere-plan`: plan or adjust a week.
- `trenere-lint`: check wiki health.

## Output Format

Use a short answer:

```md
Short answer: ...

Why:
- ...

Do now:
- ...

Next Trenere step: ...
```

If files changed, include files changed and commit status.

## Edge Cases

- If onboarding is missing, answer cautiously and suggest `trenere-onboard`.
- If workouts are missing, suggest `trenere-import` or `trenere-coros-fetch`.
- If symptoms cross the medical boundary, advise stopping training and seeking
  medical evaluation; do not diagnose.
- If there is no durable learning, do not create filler updates.

## Git/Log/Index Update Rules

- Do not write files for ordinary Q&A.
- Log meaningful coaching decisions in `{athlete-data-root}/wiki/log.md`.
- Commit only meaningful updates, in the correct repo.
- For explicit self-improvement persistence, commit and push affected public and
  private repos separately.
