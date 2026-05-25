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
- [Principles](principles/training_principles.md) — operational coaching rules
  for sustainable recreational-runner development.
- [Workout Types](workouts/easy_runs.md) — purpose, use, and guardrails for
  common running sessions.
- [Runner Profiles](runner_profiles/beginner.md) — default programming by
  runner level and context.
- [Programming](programming/weekly_structure_examples.md) — weekly structures,
  progression, deloads, and return-from-niggle rules.
- [Evidence Summary](evidence/evidence_summary.md) — confidence labels and
  claims that should stay conservative.

## Principle Pages

- [Training Principles](principles/training_principles.md) — core coaching
  philosophy and decision rules.
- [Intensity Distribution](principles/intensity_distribution.md) — easy/hard
  balance and gray-zone control.
- [Volume And Mileage](principles/volume_and_mileage.md) — progression rules
  and approximate workload ranges.
- [Periodization](principles/periodization.md) — simple phase structure for
  recreational runners.
- [Recovery And Adaptation](principles/recovery_and_adaptation.md) — fatigue,
  sleep, HRV, and life-stress rules.
- [Injury Prevention](principles/injury_prevention.md) — niggle triage and
  return sequence.
- [Strength Training](principles/strength_training.md) — minimum effective dose
  and placement around running.

## Workout Pages

- [Easy Runs](workouts/easy_runs.md) — aerobic and recovery-compatible running.
- [Long Runs](workouts/long_runs.md) — endurance, durability, and fueling.
- [Threshold Runs](workouts/threshold_runs.md) — controlled sustained hard
  work.
- [VO2max Intervals](workouts/vo2max_intervals.md) — costly high-end aerobic
  work.
- [Hill Repeats](workouts/hill_repeats.md) — uphill strength and mechanics.
- [Strides](workouts/strides.md) — low-fatigue speed maintenance.
- [Progression Runs](workouts/progression_runs.md) — controlled finish-fast
  sessions.
- [Fartlek](workouts/fartlek.md) — flexible effort-based speed variation.
- [Race-Specific Workouts](workouts/race_specific_workouts.md) — preparation
  for exact race demands.
- [Trail / Ultra Specific](workouts/trail_ultra_specific.md) — terrain,
  elevation, hiking, fueling, and downhill load.

## Programming Pages

- [Weekly Structure Examples](programming/weekly_structure_examples.md) —
  simple templates by level.
- [Yearly Periodization Examples](programming/yearly_periodization_examples.md)
  — reset, base, build, race-specific, taper, and recovery examples.
- [Progression Rules](programming/progression_rules.md) — how to add stress.
- [Deload Rules](programming/deload_rules.md) — when and how to reduce load.
- [Return From Niggle Rules](programming/return_from_niggle_rules.md) — staged
  return to normal training.

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
