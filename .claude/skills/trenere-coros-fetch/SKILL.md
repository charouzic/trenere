---
name: trenere-coros-fetch
description: Fetch read-only training data from the configured COROS MCP server and stage it as raw source material for Trenere import, review, or planning.
---

# trenere-coros-fetch

## Athlete Data Root

Before reading or writing private coaching data, resolve `{athlete-data-root}` using `AGENTS.md`: `TRENERE_ATHLETE_DATA`, then `.trenere-athlete-data`, then `../trenere-athlete-data`. Public core files are read from this repository; athlete-specific profile, logs, workouts, imports, blocks, insights, and sync notes are read or written under `{athlete-data-root}`. Commit public core changes in this repo and private athlete-data changes from `{athlete-data-root}`.


## When To Use

Use this skill when the athlete asks to get data from COROS, sync recent COROS
workouts, inspect COROS recovery/fitness metrics, download original activity
files, inspect planned workout targets, or stage COROS data before running
`/trenere-import`, `/trenere-review`, or `/trenere-plan`.

This skill is read-only. It does not write to COROS, create workouts, update a
COROS calendar, or require COROS for the rest of Trenere to work.

## Inputs Required

- requested data type: workouts, sleep, HRV, recovery, fitness assessment,
  training load, resting HR, average HR, stress, devices, training schedule, or
  planned workout target zones
- date range or recent-day count when relevant
- optional sport filter for workouts
- whether to only summarize data or also stage source notes under `{athlete-data-root}/raw/imports/`

If no date range is provided, default to the last 14 days for workouts and the
last 7 days for health/recovery trends.

Default behavior is persistent: when COROS returns concrete records, preserve
them in the private athlete-data repo unless the athlete explicitly asks for a
no-write summary.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`
- `{athlete-data-root}/wiki/meta/last-sync.md`
- `{athlete-data-root}/wiki/log.md`
- `{athlete-data-root}/raw/imports/README.md`
- `wiki/data/training_data_interpretation.md` when interpreting HR, pace, power,
  GPS, elevation, FIT, laps, or planned-vs-actual execution
- `.claude/skills/trenere-import/SKILL.md` if the next step is wiki import

## Files To Update

- `{athlete-data-root}/raw/imports/coros/YYYY-MM-DD-*.md` when staging COROS source material
- `{athlete-data-root}/raw/imports/coros/fit/{labelId}.fit` when a workout
  record has a labelId and the original FIT file can be downloaded
- `{athlete-data-root}/wiki/workouts/YYYY-MM.md` when workout records include
  enough fields for robust entries
- `{athlete-data-root}/wiki/meta/last-sync.md` when data is fetched or staged
- `{athlete-data-root}/wiki/log.md`
- `wiki/index.md` only if current import/sync status should change

For workout records, staging alone is not enough when the returned data has
date, sport/type, duration, and distance. Import those records into the monthly
workout wiki in the same turn, or explicitly invoke/follow `/trenere-import`.
When records include `labelId` and `sportType`, also try to preserve the original
FIT file because MCP detail may omit laps, records, and execution granularity.

## COROS MCP Source

The expected Codex MCP server is:

```text
Name: coros
URL: https://mcpeu.coros.com/mcp
Mode: read-only
Auth: OAuth
```

If the `coros` MCP tools are not available in the current Codex session, tell the
athlete to restart Codex after confirming `codex mcp get coros` shows the server
as enabled. If auth has expired, use `codex mcp login coros`.

## Supported Data

Use whatever COROS MCP tools are exposed in the current session. Common useful
queries include:

- workout records by date range and sport
- workout detail for a selected activity
- recovery status
- training load assessment
- fitness assessment overview
- sleep data
- HRV assessment
- resting heart rate
- average heart rate
- stress level
- training schedule
- planned workout target zones, when exposed by the current connector or by
  authenticated COROS web responses supplied by the athlete
- devices

Do not assume every tool is available. If a requested data type is unavailable,
say so and offer the closest available read-only alternative.

## Original Activity File Flow

COROS MCP activity detail may omit laps, splits, intervals, and workout blocks.
For workout records with `labelId` and `sportType`, use MCP to discover the
activity ids, then try to preserve the original FIT file for detailed analysis.

Known download endpoint pattern:

```text
https://teameuapi.coros.com/activity/detail/download?labelId={labelId}&sportType={sportType}&fileType=4
```

`fileType=4` has been observed to return a FIT `fileUrl`. The endpoint may
require an authenticated COROS web token. If it returns a `fileUrl`, download
that URL into:

```text
{athlete-data-root}/raw/imports/coros/fit/{labelId}.fit
```

Prefer FIT for Trenere because it is the device-native format and usually
retains the richest data. Use TCX only as a fallback when FIT is unavailable or
when XML parsing is specifically useful.

If the athlete already knows the FIT `fileUrl`, download it directly. Do not
store access tokens, cookies, or personal COROS account IDs in the public core
repo. If a personal id or token is needed for automation, keep it only in
ignored private config under `{athlete-data-root}`.

If the download endpoint requires COROS web authentication, read the token only
from the local environment:

```text
COROS_ACCESS_TOKEN=<token>
```

Send it as the HTTP header:

```text
accesstoken: $COROS_ACCESS_TOKEN
```

Treat this as an optional supplement, not a V1 dependency. Do not hardcode the
token in source, staged markdown, logs, shell history, or committed config. The
token appears opaque rather than JWT-like, so do not infer expiry from its
format. If COROS returns `401` or `403`, assume the token is expired or invalid,
stop authenticated web calls, and ask the athlete to refresh authentication or
provide a new token. If COROS login/storage metadata includes an explicit
`expiresIn`, `expireTime`, `expiration`, `refreshToken`, or
`tokenExpireTime`, record only the non-secret expiry fact in private sync notes.

If the authenticated download endpoint is unavailable but a previously observed
FIT `fileUrl` is known, it is acceptable to try that URL directly. If download
fails, continue with MCP summary data and record that original-file download was
not available.

When a FIT file is available, inspect it for `lap`, `record`, `session`, and
developer-data messages. FIT is usually the richer device-native source.

Use original-file-derived lap/block data for execution review when available,
and mark whether the granularity came from FIT, fallback TCX, or MCP.

## Planned Target Zone Flow

COROS MCP training schedule may only return summary fields such as session name,
estimated duration, distance, and load. Do not claim target HR, pace, power, or
step ranges are available unless they are present in the returned data.

For planned workout targets, preserve these fields when available:

- `planId`, `programId`, `labelId`, `sportType`, date, and session name
- `exercises` or workout steps, including group/repeat structure
- `exerciseType`, `targetType`, `targetValue`, `restType`, and `restValue`
- `intensityType`, `hrType`, `intensityValue`, `intensityValueExtend`,
  `intensityPercent`, `intensityPercentExtend`, `isIntensityPercent`, and
  `intensityCustom`
- unit and zone context needed to interpret percent-based targets

Known COROS web app concepts observed in the Training Hub bundle:

- `targetType`: `2=time`, `4=heart`, `5=distance`, `6=load`
- `intensityType`: `2=heart`, `3=pace`, `4=speed`, `6=power`, `7=cadence`,
  `11=RPE`
- schedule query endpoint observed for planned workouts:
  `/training/schedule/query?startDate=YYYYMMDD&endDate=YYYYMMDD&supportRestExercise=1`
- plan/program detail endpoints used by the web app include
  `/training/plan/detail` and `/training/program/detail`

Those web endpoints require authenticated COROS web access and are outside the
read-only MCP contract unless the current environment already exposes them or
the athlete supplies an authenticated response/export. If authenticated web
calls are explicitly used, pass `COROS_ACCESS_TOKEN` as the `accesstoken` header
and follow the token-handling rules above. Never store auth tokens, cookies, or
session headers. If only MCP schedule summaries are available, stage the planned
workout summary and explicitly mark target zones as `not provided`.

When both planned targets and completed FIT/lap data are available, stage both
sources and compare planned blocks to actual execution conservatively. If FIT
laps do not match planned blocks exactly, say so instead of forcing a false
interval-by-interval comparison.

## Planned-vs-Actual Fast Path

Use this path when the athlete asks to compare a completed workout against its
scheduled/programmed blocks, especially HR-, pace-, or power-limited blocks.

Required identifiers:

- workout date
- `labelId`
- `sportType`

If the athlete gives only a date, first use COROS MCP workout records or the
existing workout wiki to resolve `labelId` and `sportType`.

1. Fetch the planned workout summary with COROS MCP schedule for the date.
2. If `COROS_ACCESS_TOKEN` is present, fetch richer schedule data from:

```text
https://teameuapi.coros.com/training/schedule/query?startDate=YYYYMMDD&endDate=YYYYMMDD&supportRestExercise=1
```

with header:

```text
accesstoken: $COROS_ACCESS_TOKEN
```

3. Match the schedule `entities[].labelId` to the completed workout `labelId`.
4. From the matched `programs[].exercises`, preserve only the fields needed for
   analysis:
   - block order: `sortNo`
   - block kind: `exerciseType`, `name`, `overview`
   - duration/distance target: `targetType`, `targetValue`
   - intensity target: `intensityType`, `hrType`, `intensityValue`,
     `intensityValueExtend`, `intensityPercent`, `intensityPercentExtend`,
     `isIntensityPercent`, `intensityCustom`
   - grouping: `isGroup`, `groupId`, `sets`
5. Download or reuse the original FIT file for the activity.
6. Parse FIT `record` messages for timestamp, heart rate, power, pace/speed,
   distance, and cadence as available. Use temporary/local parser tooling if
   present; do not add parser dependencies to the public repo.
7. If FIT contains `workout_step` messages, use them for alignment. If it does
   not, align planned blocks by elapsed time from the first FIT record.
8. For each planned block, report:
   - planned target and duration/distance
   - actual elapsed window
   - actual distance
   - average and max value for the target metric
   - time and percent below, inside, and above target range
9. Report extra time after the planned workout as `not planned`.
10. Preserve the comparison under:

```text
{athlete-data-root}/raw/imports/coros/YYYY-MM-DD-workout-YYYY-MM-DD-planned-target-comparison.md
```

11. Update the monthly workout entry with a compact comparison table and coaching
    read.
12. Update `{athlete-data-root}/wiki/meta/last-sync.md` and append a compact
    `{athlete-data-root}/wiki/log.md` entry.

If `COROS_ACCESS_TOKEN` is absent or invalid, do not attempt browser/session
workarounds by default. Use MCP summaries and the FIT file, mark planned targets
as `not provided`, and tell the athlete that a fresh token is needed for block
targets.

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Confirm the requested COROS data type and date range from the user prompt.
3. Use the configured `coros` MCP tools in read-only mode.
4. Preserve raw values and uncertainty. Do not invent missing fields.
5. For workout records, capture `labelId` and `sportType` for each activity when
   available.
6. For each workout with `labelId` and `sportType`, try to download the original
   FIT file into `{athlete-data-root}/raw/imports/coros/fit/{labelId}.fit`.
7. If a FIT file is downloaded, inspect it for message counts and lap summaries.
   Do not require a parser dependency in the public repo; use available local
   tooling or summarize that FIT was preserved for later parsing.
8. Interpret device metrics conservatively using
   `wiki/data/training_data_interpretation.md`; do not overfit one workout.
9. Summarize the fetched data in plain language.
10. Create a dated markdown file under
   `{athlete-data-root}/raw/imports/coros/`.
11. Include source, fetch date, date range, query parameters, returned records,
    label IDs, FIT download status, and FIT-derived lap summaries when available
    in the staged file.
12. If planned target zones or planned-vs-actual comparison are requested, follow
    the Planned-vs-Actual Fast Path. Prefer MCP first for identity/summary, then
    use `COROS_ACCESS_TOKEN` for richer schedule blocks when present. Mark target
    zones `not provided` when MCP/web data does not expose them.
13. If the returned data is workout records and includes enough fields, import or
   update entries in `{athlete-data-root}/wiki/workouts/YYYY-MM.md` using the
   public workout format.
14. Use `not provided` or `unknown` for missing fields. Do not invent elevation,
   RPE, subjective notes, HRV, resting HR, or sleep.
15. Update `{athlete-data-root}/wiki/meta/last-sync.md` with the COROS fetch
    date, range, staged file path, FIT file paths when available, and import
    status.
16. Append a `sync` or `import` entry to `{athlete-data-root}/wiki/log.md`.
17. Recommend the next skill:
    - `/trenere-review` to analyze imported data
    - `/trenere-plan` when the fetched data is enough for planning context
    - `/trenere-import` only if staging succeeded but import could not be done
17. Show changed files before committing if asked to commit.

## Staged File Format

Use this format for files under `{athlete-data-root}/raw/imports/coros/`:

```md
# COROS Fetch — YYYY-MM-DD

- Source: COROS MCP `coros`
- Fetched at: YYYY-MM-DD HH:MM local time
- Requested range: YYYY-MM-DD to YYYY-MM-DD
- Data type: workouts / sleep / HRV / recovery / other
- Query notes: filters, sport types, limits, or assumptions

## Summary

Short factual summary.

## Records

Raw or lightly normalized records. Preserve dates, labels, IDs, sport types,
duration, distance, elevation, pace/speed, HR, power, RPE, and notes when
available.

## Original Files

FIT files downloaded or attempted. Include `labelId`, `sportType`, file path,
download status, parser/message counts, and lap summaries when available.

## Planned Targets

Schedule, plan, or program target fields returned by COROS. Include planned
steps, target type/value, intensity type/ranges, units, and missing target-zone
notes. Use `not provided` when only schedule summaries are available.

## Planned-vs-Actual Comparison

For each planned block, include the planned target, actual elapsed window,
actual distance, average/max target metric, and time/percent below, inside, and
above target. Include extra time after the planned workout as `not planned`.

## Import Notes

Notes for `/trenere-import`, including missing fields, likely duplicates, and
any injury/fatigue flags.
```

## Output Format

Return:

- COROS data type fetched
- date range
- number of records or days returned
- staged file path, if created
- workout wiki file updated, if imported
- FIT file paths and lap/record counts, if downloaded; TCX path only if FIT was
  unavailable or deliberately used as fallback
- planned target-zone fields found or `not provided`
- important missing fields or uncertainty
- safety/fatigue flags noticed
- recommended next skill

## Edge Cases

- If COROS MCP is unavailable, expired, or not authorized, do not block Trenere.
  Explain the issue and fall back to pasted data or files under `{athlete-data-root}/raw/imports/`.
- If FIT download fails, still stage and import the MCP workout summary, and log
  that original-file detail was unavailable.
- If COROS returns more data than needed, stage and import only the requested
  range unless the user asks for a broader export.
- If records appear duplicated against existing wiki workouts, note likely
  duplicates in the staged file and update/merge missing fields conservatively;
  do not create duplicate workout entries and do not delete anything.
- If COROS data includes symptoms covered by the safety boundary in `AGENTS.md`,
  advise stopping training and seeking medical evaluation.
- If the user asks to write to COROS, decline for V1 and explain that this skill
  is read-only.
- If the user asks for medical diagnosis from COROS metrics, do not diagnose.

## Git/Log/Index Update Rules

- Append to `{athlete-data-root}/wiki/log.md` using:

```md
## [YYYY-MM-DD] sync | COROS data fetched

Short notes.
```

- Update `{athlete-data-root}/wiki/meta/last-sync.md` whenever COROS data is fetched.
- Update `wiki/index.md` only when current sync/import status changes.
- Commit only after meaningful updates.
- Default commit:

```bash
git add .
git commit -m "trenere-coros-fetch: fetch coros data YYYY-MM-DD"
```
