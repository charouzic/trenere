---
name: trenere-plan
description: Plan the next training week from athlete context, recent training, constraints, and coaching directives.
---

# trenere-plan

## When To Use

Use for next-week planning, near-term schedules, or modifications to an existing
plan.

## Agent Decision Rules

- Build the plan from constraints, recent training, recovery signals, and
  directives before adding workouts.
- Choose one primary weekly goal and make every session support or protect it.
- Count hard sessions honestly: threshold, tempo, intervals, hills, races,
  fast-finish long runs, and demanding trail long runs all count.
- When context is missing, produce a cautious starter plan and name the missing
  inputs instead of inventing fitness.
- Do not cram missed workouts into the future week.
- Include fallback rules that tell the athlete exactly when to reduce, swap, or
  skip a session.
- Write block files only for meaningful multi-week plans, not every ad hoc
  schedule.

## Inputs Required

- target week/date range
- available training days and fixed constraints
- current goal
- recent workouts or review
- fatigue, sleep, stress, illness, and injury flags

Use wiki context and mark assumptions when inputs are missing.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `wiki/coach-knowledge.md`
- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/preferences.md`
- `{athlete-data-root}/wiki/profile/injury-history.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`
- recent `{athlete-data-root}/wiki/workouts/YYYY-MM.md`
- recent `{athlete-data-root}/wiki/log.md`
- relevant `{athlete-data-root}/wiki/insights/*.md`

## Files To Update

- `{athlete-data-root}/wiki/log.md`
- `{athlete-data-root}/wiki/blocks/*.md` only for meaningful block plans
- `{athlete-data-root}/wiki/profile/coaching-directives.md` only for durable new
  directives

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Review recent training, constraints, and directives.
3. Choose the conservative runner-level default from `wiki/coach-knowledge.md`.
4. Set one weekly goal.
5. Build a day-by-day schedule.
6. Include purpose, intensity guidance, fallback rules, and feedback to report.
7. Count hard sessions honestly, including hilly/fast long runs.
8. Reduce intensity first when sleep, stress, soreness, illness, or injury flags
   are poor.
9. Append a compact `plan` entry to the private log.

## Output Format

- planning week
- weekly goal
- day-by-day plan
- intensity guidance
- fallback rules
- key feedback to report
- assumptions/uncertainties

## Edge Cases

- If injury flags are active, reduce intensity and volume until risk is clearer.
- If safety-boundary symptoms are reported, advise stopping training and seeking
  medical evaluation.
- If recent training is unknown, use a cautious starter week and ask for import.
- Do not cram missed workouts.

## Git/Log/Index Update Rules

Log format:

```md
## [YYYY-MM-DD] plan | Next week planned

Short notes.
```

Commit only meaningful updates.
