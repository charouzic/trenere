---
name: trenere-coros-fetch
description: Fetch read-only COROS data and preserve it as source material for Trenere import, review, or planning.
---

# trenere-coros-fetch

## When To Use

Use when the athlete asks to get COROS workouts, health/recovery metrics,
training schedule, original activity files, or planned-vs-actual workout
execution. This skill is read-only and optional; Trenere must still work without
COROS.

## Inputs Required

- data type: workouts, detail, schedule, sleep, HRV, recovery, training load,
  fitness, HR, stress, devices, or planned-vs-actual
- date range or recent-day count
- optional sport filter
- whether the athlete explicitly wants no file writes

Default: last 14 days for workouts, last 7 days for health/recovery.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `wiki/coach-knowledge.md`
- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`
- `{athlete-data-root}/wiki/meta/last-sync.md`
- `{athlete-data-root}/wiki/log.md`
- `{athlete-data-root}/raw/imports/README.md`
- `.claude/skills/trenere-import/SKILL.md` if importing fetched workouts

## Files To Update

- `{athlete-data-root}/raw/imports/coros/YYYY-MM-DD-*.md`
- `{athlete-data-root}/raw/imports/coros/fit/{labelId}.fit` when available
- `{athlete-data-root}/wiki/workouts/YYYY-MM.md` when records are usable
- `{athlete-data-root}/wiki/meta/last-sync.md`
- `{athlete-data-root}/wiki/log.md`

If COROS returns concrete workout records, preserve/import them unless the
athlete explicitly asked for no writes.

## COROS Sources

- Preferred: configured COROS MCP tools in read-only mode.
- Expected MCP server: `coros`, `https://mcpeu.coros.com/mcp`.
- Optional web token: `COROS_ACCESS_TOKEN`, sent as header
  `accesstoken: $COROS_ACCESS_TOKEN`.
- Agent rule: for planned-vs-actual review, planned blocks, target ranges,
  schedule inspection, or future workout-building features, check for
  `COROS_ACCESS_TOKEN` before concluding that COROS cannot provide the needed
  data.
- The authenticated web API can expose planned workout blocks, target ranges,
  and schedule data that may not be present in MCP summaries or exported FIT
  files.
- If the token is missing or returns `401`/`403`, continue with MCP, staged
  files, or pasted data and state the limitation.
- Never store tokens, cookies, auth headers, or session data.

## FIT And Planned Blocks

- Preserve FIT when possible; it is preferred over TCX.
- Known observed download endpoint:
  `https://teameuapi.coros.com/activity/detail/download?labelId={labelId}&sportType={sportType}&fileType=4`
- The download endpoint may return JSON with `data.fileUrl` instead of FIT bytes
  directly. When response `content-type` is JSON or body starts with `{`, parse
  `data.fileUrl`, download that URL, and validate the result as FIT before
  preserving it.
- Validate FIT downloads by checking for `.FIT` magic at byte offset 8. Do not
  preserve JSON/error bodies with a `.fit` extension.
- Planned schedule endpoint observed:
  `https://teameuapi.coros.com/training/schedule/query?startDate=YYYYMMDD&endDate=YYYYMMDD&supportRestExercise=1`
- Use MCP first to resolve date, `labelId`, and `sportType`; use the web token
  only for richer schedule/download data when present.
- Preserve planned block fields needed for review: block order/name,
  duration/distance target, intensity type/range, grouping/repeats.
- For planned-vs-actual, align by FIT `workout_step` when present; otherwise by
  elapsed time only when planned durations are known.
- Report below/in/above target time, extra unplanned time, and alignment method.

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Query COROS MCP for the requested data.
3. Preserve raw values and missing fields; do not invent precision.
4. For workouts, capture date, type, duration, distance, elevation, HR/pace/power,
   notes, `labelId`, and `sportType` when available.
5. Try to preserve FIT files for activities with IDs.
6. If planned-vs-actual is requested, fetch planned blocks when auth allows and
   compare conservatively against FIT records/laps.
7. Stage a compact source note under `{athlete-data-root}/raw/imports/coros/`.
8. Import usable workout records into `{athlete-data-root}/wiki/workouts/YYYY-MM.md`.
9. Update last-sync and append a compact private log entry.
10. Recommend review or plan as the next skill.

## Staged File Format

```md
# COROS Fetch — YYYY-MM-DD

- Source:
- Fetched at:
- Range:
- Data type:

## Summary

## Records

## Original Files

## Planned Targets

## Planned-vs-Actual

## Import Notes
```

Keep staged notes factual and compact.

## Output Format

- data fetched and range
- records/days returned
- staged file path
- workout wiki file updated, if any
- FIT paths/counts, if any
- planned targets found or `not provided`
- key uncertainty
- next skill

## Edge Cases

- MCP unavailable/auth expired: fall back to pasted data or files.
- FIT download fails: still stage/import MCP summaries.
- Duplicates: merge missing fields; do not duplicate entries.
- Medical symptoms: apply `AGENTS.md` safety boundary.
- Write-to-COROS workout requests: route to `trenere-coros-workout`; this skill
  remains read-only.

## Git/Log/Index Update Rules

Log format:

```md
## [YYYY-MM-DD] sync | COROS data fetched

Short notes.
```

Commit only meaningful updates.
