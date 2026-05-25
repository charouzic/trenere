# Trenere Wiki Index

This is the public-safe navigation layer for the Trenere core. Athlete-specific
coaching memory lives in the private athlete-data repo, defaulting to
`../trenere-athlete-data`.

## Start Here

- [Trenere Ask Skill](../.claude/skills/trenere-ask/SKILL.md) — general
  entrypoint for questions, uncertainty, advice, load adjustments, and routing.
- [Athlete Data Template](../templates/athlete-data/README.md) — bootstrap
  structure for the private athlete-data repo.
- Private athlete profile — `{athlete-data-root}/wiki/profile/athlete.md`.
- Private coaching directives —
  `{athlete-data-root}/wiki/profile/coaching-directives.md`.
- Private training log — `{athlete-data-root}/wiki/log.md`.

## Training Memory

- [Workouts](workouts/README.md) — workout file conventions and month-level
  pages. Actual monthly athlete workout files belong in the private
  athlete-data repo.
- Private blocks — `{athlete-data-root}/wiki/blocks/`.
- Private insights — `{athlete-data-root}/wiki/insights/`.

## Knowledge

- [Training Principles](knowledge/training-principles.md) — general coaching
  principles used in Trenere decisions.
- [Session Library](knowledge/session-library.md) — reusable workout patterns.

## Private Meta

- Last sync — `{athlete-data-root}/wiki/meta/last-sync.md`.
- Raw imports — `{athlete-data-root}/raw/imports/`.

## Current Status

- Athlete onboarding: not completed.
- Recent workout import: none yet.
- Current planning horizon: unknown.
- Known safety flags: unknown.
- Optional COROS MCP: can be configured locally as `coros`; use
  `trenere-coros-fetch` for read-only staging before import.
