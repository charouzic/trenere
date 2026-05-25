---
name: trenere-coros-fetch
description: Fetch read-only training data from the configured COROS MCP server and stage it as raw source material for Trenere import, review, or planning.
---

# trenere-coros-fetch

## Athlete Data Root

Before reading or writing private coaching data, resolve `{athlete-data-root}` using `AGENTS.md`: `TRENERE_ATHLETE_DATA`, then `.trenere-athlete-data`, then `../trenere-athlete-data`. Public core files are read from this repository; athlete-specific profile, logs, workouts, imports, blocks, insights, and sync notes are read or written under `{athlete-data-root}`. Commit public core changes in this repo and private athlete-data changes from `{athlete-data-root}`.


## When To Use

Use this skill when the athlete asks to get data from COROS, sync recent COROS
workouts, inspect COROS recovery/fitness metrics, or stage COROS data before
running `/trenere-import`, `/trenere-review`, or `/trenere-plan`.

This skill is read-only. It does not write to COROS, create workouts, update a
COROS calendar, or require COROS for the rest of Trenere to work.

## Inputs Required

- requested data type: workouts, sleep, HRV, recovery, fitness assessment,
  training load, resting HR, average HR, stress, devices, or training schedule
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
- `.claude/skills/trenere-import/SKILL.md` if the next step is wiki import

## Files To Update

- `{athlete-data-root}/raw/imports/coros/YYYY-MM-DD-*.md` when staging COROS source material
- `{athlete-data-root}/wiki/workouts/YYYY-MM.md` when workout records include
  enough fields for robust entries
- `{athlete-data-root}/wiki/meta/last-sync.md` when data is fetched or staged
- `{athlete-data-root}/wiki/log.md`
- `wiki/index.md` only if current import/sync status should change

For workout records, staging alone is not enough when the returned data has
date, sport/type, duration, and distance. Import those records into the monthly
workout wiki in the same turn, or explicitly invoke/follow `/trenere-import`.

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
- devices

Do not assume every tool is available. If a requested data type is unavailable,
say so and offer the closest available read-only alternative.

## Original Activity File Workaround

COROS MCP activity detail may omit laps, splits, intervals, and workout blocks.
When block-level execution matters, try to preserve the original activity file.

Known pattern:

```text
https://teameuapi.coros.com/activity/detail/download?labelId={labelId}&sportType={sportType}&fileType=4
```

That endpoint may require an authenticated COROS web token. If the endpoint
returns a `fileUrl`, download that URL into an appropriate raw import folder,
for example:

```text
{athlete-data-root}/raw/imports/coros/fit/{labelId}.fit
{athlete-data-root}/raw/imports/coros/tcx/{labelId}.tcx
```

If the athlete already knows the `fileUrl`, download it directly. Do not store
access tokens, cookies, or personal COROS account IDs in the public core repo.
If a personal id or token is needed for automation, keep it only in ignored
private config under `{athlete-data-root}`.

When a FIT file is available, inspect it for `lap`, `record`, `session`, and
developer-data messages. FIT is usually the richer device-native source.

When a TCX file is available, inspect it for `Lap`, `Trackpoint`, `Extensions`,
and `Workout` nodes. TCX is easier to parse and useful for laps/trackpoints, but
may omit proprietary fields and workout-step targets.

Use original-file-derived lap/block data for execution review when available,
and mark whether the granularity came from FIT, TCX, or MCP.

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Confirm the requested COROS data type and date range from the user prompt.
3. Use the configured `coros` MCP tools in read-only mode.
4. Preserve raw values and uncertainty. Do not invent missing fields.
5. Summarize the fetched data in plain language.
6. Create a dated markdown file under
   `{athlete-data-root}/raw/imports/coros/`.
7. Include source, fetch date, date range, query parameters, and returned records
   in the staged file.
8. If the returned data is workout records and includes enough fields, import or
   update entries in `{athlete-data-root}/wiki/workouts/YYYY-MM.md` using the
   public workout format.
9. Use `not provided` or `unknown` for missing fields. Do not invent elevation,
   RPE, subjective notes, HRV, resting HR, or sleep.
10. Update `{athlete-data-root}/wiki/meta/last-sync.md` with the COROS fetch
    date, range, staged file path, and import status.
11. Append a `sync` or `import` entry to `{athlete-data-root}/wiki/log.md`.
12. Recommend the next skill:
    - `/trenere-review` to analyze imported data
    - `/trenere-plan` when the fetched data is enough for planning context
    - `/trenere-import` only if staging succeeded but import could not be done
13. If the athlete asks about workout-block execution and an original activity
    file can be downloaded, save it under `{athlete-data-root}/raw/imports/coros/`
    and extract lap/block summaries into the workout entry or staged notes.
14. Show changed files before committing if asked to commit.

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
- FIT/TCX file path and lap/record/trackpoint counts, if downloaded
- important missing fields or uncertainty
- safety/fatigue flags noticed
- recommended next skill

## Edge Cases

- If COROS MCP is unavailable, expired, or not authorized, do not block Trenere.
  Explain the issue and fall back to pasted data or files under `{athlete-data-root}/raw/imports/`.
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
