---
name: trenere-onboard
description: Set up or update the athlete profile using minimal or optional deep onboarding.
---

# trenere-onboard

## When To Use

Use when starting Trenere for an athlete or updating goals, constraints,
preferences, zones/efforts, injury flags, or training background.

## Agent Decision Rules

- Capture minimal onboarding first; deep background is optional and should not
  block a useful V1 profile.
- Record only athlete-supplied or clearly sourced facts.
- Use `unknown`, `not provided`, or `estimated` instead of filling gaps with
  generic runner assumptions.
- Put current state in `athlete.md`, standing preferences in `preferences.md`,
  injury facts in `injury-history.md`, and durable rules in
  `coaching-directives.md`.
- Do not turn one-off preferences, guesses, or temporary constraints into
  directives.
- Apply the medical boundary before collecting more training detail if symptoms
  indicate safety risk.

## Inputs Required

Minimal onboarding:

- current goal
- current weekly volume
- available training days
- fixed constraints
- known zones or rough easy/threshold efforts
- current niggles/injury flags
- recent race/event
- last 2-4 weeks training summary

Optional deep onboarding later:

- sport background
- race history
- block history
- what worked / did not work
- long-term physiological patterns

Do not require COROS, GitHub, watch zones, or full training history.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/preferences.md`
- `{athlete-data-root}/wiki/profile/injury-history.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`
- `{athlete-data-root}/wiki/log.md`

Resolve `{athlete-data-root}` via `AGENTS.md`.

## Files To Update

- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/preferences.md`
- `{athlete-data-root}/wiki/profile/injury-history.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`
- `{athlete-data-root}/wiki/log.md`

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Use minimal or deep mode; ask only for missing fields in that mode.
3. Record supplied facts plainly; use `unknown` or `not provided` for gaps.
4. Update `athlete.md` with current goal, volume, days, constraints, efforts,
   recent event, and recent training.
5. Update `preferences.md` only for stated schedule, logistics, style, or
   feedback preferences.
6. Update `injury-history.md` with athlete-reported issues. Do not diagnose.
7. Update `coaching-directives.md` only for durable standing rules.
8. Append a compact `onboard` entry to the private log.

## Output Format

- onboarding mode
- files updated
- facts captured
- unknowns worth filling later
- safety flags, if any
- next skill, usually `trenere-import` or `trenere-plan`

## Edge Cases

- Safety-boundary symptoms: advise stopping training and seeking medical
  evaluation.
- Vague history: record as approximate instead of inventing totals.
- Unknown zones: use rough effort anchors.
- Sensitive injury details: keep notes factual and conservative.
- Deep onboarding requested immediately: still capture minimal fields first.

## Git/Log/Index Update Rules

Log format:

```md
## [YYYY-MM-DD] onboard | Profile updated

Short notes.
```

Commit only meaningful updates in the private athlete-data repo.
