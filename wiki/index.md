# Trenere Wiki Index

Public core navigation. Athlete-specific memory belongs in the private
athlete-data repo, defaulting to `../trenere-athlete-data`.

## Public Core

- [Coach Knowledge](coach-knowledge.md) — compact general coaching rules.
- [Workout Format](workouts/README.md) — monthly workout entry format.
- [Athlete Data Template](../templates/athlete-data/README.md) — private repo
  scaffold.
- [Trenere Ask Skill](../.claude/skills/trenere-ask/SKILL.md) — front door.
- [Trenere COROS Workout Skill](../.claude/skills/trenere-coros-workout/SKILL.md)
  — create, update, or delete planned COROS workouts.

## Private Athlete Data

- Profile: `{athlete-data-root}/wiki/profile/athlete.md`
- Directives: `{athlete-data-root}/wiki/profile/coaching-directives.md`
- Workouts: `{athlete-data-root}/wiki/workouts/YYYY-MM.md`
- Log: `{athlete-data-root}/wiki/log.md`
- Raw imports: `{athlete-data-root}/raw/imports/`

## Status

- Athlete onboarding: private repo dependent.
- Recent workout import: private repo dependent.
- Optional COROS MCP: use `trenere-coros-fetch` for read-only data and
  `trenere-coros-workout` for explicit workout writes; V1 still works without
  COROS.
