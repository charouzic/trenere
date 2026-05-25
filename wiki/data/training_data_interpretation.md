# Training Data Interpretation

## Purpose

Use device data for better coaching decisions without pretending it is more
precise than it is.

## Core Rules

- Interpret HR, pace, power, GPS, elevation, HRV, and sleep together with
  subjective effort and context.
- Do not overfit one workout.
- Prefer trends over single readings.
- Preserve raw values when importing; summarize only after source data is saved.
- Mark missing or uncertain fields as `not provided`, `unknown`, or `estimated`.

## Metric Rules

- Heart rate lags effort, especially early in intervals and on hills.
- Heart rate can drift with heat, dehydration, fatigue, caffeine, stress, and
  illness.
- Pace is less useful on hills, trails, technical terrain, wind, heat, snow, or
  poor GPS.
- Power can help on hills and variable terrain, but device models differ.
- GPS distance and elevation can be noisy.
- FIT is preferred over TCX when available because it is usually the richer
  device-native source.
- Laps may be manual, automatic, or workout-driven; confirm before treating them
  as planned blocks.

## When To Use

- COROS, FIT, TCX, GPS, HR, pace, power, elevation, or planned-vs-actual review.
- Explaining mismatches between plan and execution.
- Deciding whether a workout was truly easy, moderate, or hard.

## When Not To Use

- To diagnose illness or medical conditions.
- To override clear subjective safety flags.

## Common Mistakes

- Treating HR zones as perfectly precise.
- Calling a workout failed because one metric missed target.
- Comparing trail pace directly with road pace.
- Ignoring extra time, downhill load, or heat.
- Treating HRV alone as a training command.

## Coach Decision Rules

- If metrics and subjective notes disagree, state the mismatch and avoid strong
  conclusions.
- For planned-vs-actual block comparisons, report alignment method: workout-step,
  lap, elapsed-time, or manual estimate.
- If FIT lacks workout-step messages, align by elapsed time only when planned
  block durations are known.
- Treat extra work after a planned session as additional load.
- Use device data to inform next-week load, not to judge the athlete.

## Evidence Notes

- HRV can help when used as trend and context: Moderate evidence.
- Exact device-derived thresholds and zones: Limited evidence.
- GPS/elevation accuracy varies by device, route, and environment.
