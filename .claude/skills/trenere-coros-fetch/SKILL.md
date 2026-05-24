---
name: trenere-coros-fetch
description: Fetch read-only training data from the configured COROS MCP server and stage it as raw source material for Trenere import, review, or planning.
---

# trenere-coros-fetch

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
- whether to only summarize data or also stage source notes under `raw/imports/`

If no date range is provided, default to the last 14 days for workouts and the
last 7 days for health/recovery trends.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `wiki/profile/athlete.md`
- `wiki/profile/coaching-directives.md`
- `wiki/meta/last-sync.md`
- `wiki/log.md`
- `raw/imports/README.md`
- `.claude/skills/trenere-import/SKILL.md` if the next step is wiki import

## Files To Update

- `raw/imports/coros/YYYY-MM-DD-*.md` when staging COROS source material
- `wiki/meta/last-sync.md` when data is fetched or staged
- `wiki/log.md`
- `wiki/index.md` only if current import/sync status should change

Do not update `wiki/workouts/YYYY-MM.md` directly unless the user explicitly asks
to fetch and import in one pass. Prefer handing staged COROS source material to
`/trenere-import`.

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

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Confirm the requested COROS data type and date range from the user prompt.
3. Use the configured `coros` MCP tools in read-only mode.
4. Preserve raw values and uncertainty. Do not invent missing fields.
5. Summarize the fetched data in plain language.
6. If staging source material, create a dated markdown file under
   `raw/imports/coros/`.
7. Include source, fetch date, date range, query parameters, and returned records
   in the staged file.
8. Update `wiki/meta/last-sync.md` with the COROS fetch date, range, and staged
   file path.
9. Append a `sync` entry to `wiki/log.md`.
10. Recommend the next skill:
    - `/trenere-import` to convert staged workout data into `wiki/workouts/`
    - `/trenere-review` to analyze already-imported data
    - `/trenere-plan` when the fetched data is enough for planning context
11. Show changed files before committing if asked to commit.

## Staged File Format

Use this format for files under `raw/imports/coros/`:

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
- important missing fields or uncertainty
- safety/fatigue flags noticed
- recommended next skill

## Edge Cases

- If COROS MCP is unavailable, expired, or not authorized, do not block Trenere.
  Explain the issue and fall back to pasted data or files under `raw/imports/`.
- If COROS returns more data than needed, summarize and stage only the requested
  range unless the user asks for a broader export.
- If records appear duplicated against existing wiki workouts, note likely
  duplicates in the staged file; do not delete anything.
- If COROS data includes symptoms covered by the safety boundary in `AGENTS.md`,
  advise stopping training and seeking medical evaluation.
- If the user asks to write to COROS, decline for V1 and explain that this skill
  is read-only.
- If the user asks for medical diagnosis from COROS metrics, do not diagnose.

## Git/Log/Index Update Rules

- Append to `wiki/log.md` using:

```md
## [YYYY-MM-DD] sync | COROS data fetched

Short notes.
```

- Update `wiki/meta/last-sync.md` whenever COROS data is fetched.
- Update `wiki/index.md` only when current sync/import status changes.
- Commit only after meaningful updates.
- Default commit:

```bash
git add .
git commit -m "trenere-coros-fetch: fetch coros data YYYY-MM-DD"
```
