---
name: trenere-onboard
description: Set up or update the athlete profile using minimal or optional deep onboarding.
---

# trenere-onboard

## When To Use

Use this skill when starting Trenere for a new athlete, refreshing the athlete
profile, or updating constraints, goals, zones, preferences, injury flags, or
training background.

## Inputs Required

Minimal onboarding is required and asks only:

- current goal
- current weekly volume
- available training days
- fixed constraints
- known zones or rough easy/threshold efforts
- current niggles/injury flags
- recent race/event
- last 2-4 weeks training summary

Deep onboarding is optional and can ask later:

- sport background
- race history
- block history
- what worked / what did not
- long-term physiological patterns

Do not require COROS, GitHub, zones from a watch, or full training history.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `wiki/profile/athlete.md`
- `wiki/profile/preferences.md`
- `wiki/profile/injury-history.md`
- `wiki/profile/coaching-directives.md`
- `wiki/log.md`

## Files To Update

- `wiki/profile/athlete.md`
- `wiki/profile/preferences.md`
- `wiki/profile/injury-history.md`
- `wiki/profile/coaching-directives.md`
- `wiki/index.md`
- `wiki/log.md`

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Determine whether the athlete is doing minimal onboarding or deep onboarding.
3. Ask only for the missing fields needed for the selected mode.
4. Record supplied facts plainly. Use `unknown` or `not provided` for gaps.
5. Update `athlete.md` with current goal, volume, days, constraints, zones or
   rough effort anchors, recent race/event, and recent training summary.
6. Update `preferences.md` with schedule, logistics, training style, and feedback
   preferences only when stated.
7. Update `injury-history.md` with athlete-reported niggles and training
   modifications. Do not diagnose.
8. Update `coaching-directives.md` only for standing rules that should guide
   future reviews or plans.
9. Update `wiki/index.md` current status fields.
10. Append an `onboard` entry to `wiki/log.md`.
11. Show changed files before committing if asked to commit.

## Output Format

Return:

- onboarding mode used
- files updated
- profile facts captured
- unknowns still worth filling later
- safety flags, if any
- suggested next skill, usually `/trenere-import` or `/trenere-plan`

## Edge Cases

- If the athlete reports chest pain, unusual breathing restriction, fever, sharp
  worsening pain, neurological symptoms, or symptoms that persist/worsen, advise
  stopping training and seeking medical evaluation.
- If the athlete gives vague training history, record it as approximate rather
  than inventing weekly totals.
- If zones are unknown, use rough effort anchors such as easy conversational,
  steady, threshold, and hard.
- If injury details are sensitive or unclear, keep notes factual and conservative.
- If the athlete wants deep onboarding immediately, still complete minimal
  onboarding first so the repo becomes useful.

## Git/Log/Index Update Rules

- Append to `wiki/log.md` using:

```md
## [YYYY-MM-DD] onboard | Profile updated

Short notes.
```

- Update `wiki/index.md` when onboarding status, current goal, safety flags, or
  planning horizon changes.
- Commit only after meaningful updates.
- Default commit:

```bash
git add .
git commit -m "trenere-onboard: update athlete profile YYYY-MM-DD"
```
