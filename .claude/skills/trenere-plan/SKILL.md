---
name: trenere-plan
description: Plan the next training week from the athlete profile, recent training, constraints, and coaching directives.
---

# trenere-plan

## Athlete Data Root

Before reading or writing private coaching data, resolve `{athlete-data-root}` using `AGENTS.md`: `TRENERE_ATHLETE_DATA`, then `.trenere-athlete-data`, then `../trenere-athlete-data`. Public core files are read from this repository; athlete-specific profile, logs, workouts, imports, blocks, insights, and sync notes are read or written under `{athlete-data-root}`. Commit public core changes in this repo and private athlete-data changes from `{athlete-data-root}`.


## When To Use

Use this skill when the athlete asks for next-week planning, a near-term schedule,
or modifications to an existing plan.

## Inputs Required

- target week or date range
- available training days
- fixed constraints
- current goal
- recent workouts or review summary
- current injury/fatigue/sleep/stress flags, if known

If some inputs are missing, use existing wiki context and mark assumptions.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/injury-history.md`
- `{athlete-data-root}/wiki/profile/preferences.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`
- recent `{athlete-data-root}/wiki/workouts/YYYY-MM.md` files
- recent `{athlete-data-root}/wiki/insights/*.md` if relevant
- `wiki/principles/training_principles.md`
- `wiki/principles/intensity_distribution.md`
- `wiki/principles/volume_and_mileage.md`
- `wiki/principles/recovery_and_adaptation.md`
- `wiki/principles/injury_prevention.md` when niggles or injury history matter
- `wiki/programming/weekly_structure_examples.md`
- `wiki/programming/progression_rules.md`
- `wiki/programming/deload_rules.md`
- `wiki/programming/return_from_niggle_rules.md` when relevant
- one relevant `wiki/runner_profiles/*.md` page
- relevant `wiki/workouts/*.md` pages for planned session types
- relevant `wiki/races/*.md` pages when a goal race is known
- `{athlete-data-root}/wiki/log.md`

## Files To Update

- `{athlete-data-root}/wiki/log.md`
- `{athlete-data-root}/wiki/blocks/*.md` only when planning or updating a meaningful block
- `wiki/index.md` if planning horizon changes
- `{athlete-data-root}/wiki/profile/coaching-directives.md` only when a durable directive emerges

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Read recent training and any recent review.
3. Select the closest runner profile and read the matching public profile page.
4. Read the public programming pages for weekly structure, progression, and
   deload rules.
5. If a race goal is known, read the relevant race-distance page.
6. Confirm or infer the planning week. State assumptions clearly.
7. Respect available days and fixed constraints.
8. Set a weekly goal.
9. Build a day-by-day schedule.
10. For each session, include purpose and intensity guidance from the relevant
    workout page.
11. Include fallback rules if tired, stressed, sore, sick, or short on time.
12. Specify what feedback to report after key sessions.
13. Avoid aggressive progressions unless recent training supports them.
14. Append a `plan` entry to `{athlete-data-root}/wiki/log.md`.
15. Update `wiki/index.md` current planning horizon.
16. Show changed files before committing if asked to commit.

## Planning Rules

- Respect constraints.
- Do not overfit to one workout.
- Progress conservatively.
- Prioritize consistency over hero sessions.
- Modify plan if sleep, stress, soreness, or injury flags are poor.
- Use beginner/intermediate/serious-hobby/trail-ultra defaults as defaults, not
  labels of athlete worth.
- Do not schedule two hard sessions unless recent recovery, injury status, and
  consistency support it.
- Count demanding long runs, hilly trail runs, downhill exposure, and fast-finish
  long runs as hard sessions.
- When the athlete has no recent speed exposure, prescribe strides before
  intervals.

## Output Format

Return:

- planning week
- weekly goal
- day-by-day schedule
- session purpose
- intensity guidance
- fallback rules
- what feedback to report after key sessions
- assumptions and uncertainties

## Edge Cases

- If injury flags are active, reduce intensity and volume until risk is clearer.
- If the athlete reports safety-boundary symptoms, advise stopping training and
  seeking medical evaluation.
- If constraints make the desired goal unrealistic, say so and plan the best
  conservative week possible.
- If recent training is unknown, use a cautious starter week and ask for workout
  import next.
- If sleep or stress is poor, keep quality optional or downgrade it to easy work.

## Git/Log/Index Update Rules

- Append to `{athlete-data-root}/wiki/log.md` using:

```md
## [YYYY-MM-DD] plan | Next week planned

Short notes.
```

- Update `wiki/index.md` current planning horizon.
- Create or update a block page only if the plan is part of a meaningful block.
- Commit only after meaningful updates.
- Default commit:

```bash
git add .
git commit -m "trenere-plan: plan next week YYYY-MM-DD"
```
