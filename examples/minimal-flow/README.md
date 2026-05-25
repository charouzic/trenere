# Minimal Trenere Flow Example

This example is public-safe and fictional. It shows the intended loop without
requiring COROS, GitHub, or a Python environment.

## 1. Onboard

Ask the agent:

```text
Use trenere-onboard for minimal onboarding.
Current goal: finish a 10K in 10 weeks.
Current weekly volume: 3 runs, about 20 km.
Available days: Tue, Thu, Sat, Sun.
Fixed constraints: work travel every other Wednesday.
Known efforts: easy is conversational, threshold is strong but controlled.
Current niggles: none.
Recent race: none.
Last 2-4 weeks: mostly easy runs, longest run 70 minutes.
```

## 2. Import

Ask the agent:

```text
Use trenere-import.
2026-05-19 easy run, 6 km, 39:00, felt controlled, RPE 3.
2026-05-22 threshold intervals, 8 km total, 4 x 5 min strong, RPE 7.
2026-05-24 long run, 11 km, 75:00, felt fine until the final hill, RPE 5.
```

## 3. Review

Ask the agent:

```text
Use trenere-review for the last week.
```

Expected review shape:

- what happened
- easy/hard balance
- what went well
- what looks risky
- what should affect next week
- whether a durable insight was filed

## 4. Plan

Ask the agent:

```text
Use trenere-plan for next week.
Available days: Tue, Thu, Sat, Sun.
```

Expected plan shape:

- weekly goal
- day-by-day schedule
- session purpose
- intensity guidance
- fallback rules
- feedback to report after key sessions
