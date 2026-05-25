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
files, or stage COROS data before running `/trenere-import`, `/trenere-review`,
or `/trenere-plan`.

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

If the authenticated download endpoint is unavailable but a previously observed
FIT `fileUrl` is known, it is acceptable to try that URL directly. If download
fails, continue with MCP summary data and record that original-file download was
not available.

When a FIT file is available, inspect it for `lap`, `record`, `session`, and
developer-data messages. FIT is usually the richer device-native source.

Use original-file-derived lap/block data for execution review when available,
and mark whether the granularity came from FIT, fallback TCX, or MCP.

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
8. Summarize the fetched data in plain language.
9. Create a dated markdown file under
   `{athlete-data-root}/raw/imports/coros/`.
10. Include source, fetch date, date range, query parameters, returned records,
    label IDs, FIT download status, and FIT-derived lap summaries when available
    in the staged file.
11. If the returned data is workout records and includes enough fields, import or
   update entries in `{athlete-data-root}/wiki/workouts/YYYY-MM.md` using the
   public workout format.
12. Use `not provided` or `unknown` for missing fields. Do not invent elevation,
   RPE, subjective notes, HRV, resting HR, or sleep.
13. Update `{athlete-data-root}/wiki/meta/last-sync.md` with the COROS fetch
    date, range, staged file path, FIT file paths when available, and import
    status.
14. Append a `sync` or `import` entry to `{athlete-data-root}/wiki/log.md`.
15. Recommend the next skill:
    - `/trenere-review` to analyze imported data
    - `/trenere-plan` when the fetched data is enough for planning context
    - `/trenere-import` only if staging succeeded but import could not be done
16. Show changed files before committing if asked to commit.

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
