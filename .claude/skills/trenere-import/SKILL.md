---
name: trenere-import
description: Bring recent workouts into the markdown wiki from pasted summaries, raw files, or optional COROS data.
---

# trenere-import

## Athlete Data Root

Before reading or writing private coaching data, resolve `{athlete-data-root}` using `AGENTS.md`: `TRENERE_ATHLETE_DATA`, then `.trenere-athlete-data`, then `../trenere-athlete-data`. Public core files are read from this repository; athlete-specific profile, logs, workouts, imports, blocks, insights, and sync notes are read or written under `{athlete-data-root}`. Commit public core changes in this repo and private athlete-data changes from `{athlete-data-root}`.


## When To Use

Use this skill when the athlete provides recent workout summaries, places source
files under `{athlete-data-root}/raw/imports/`, or explicitly wants optional COROS-sourced workouts
imported if a COROS MCP is available.

If the athlete first needs to retrieve data from COROS, use
`/trenere-coros-fetch` to stage read-only COROS source material under
`{athlete-data-root}/raw/imports/`, then return to this skill for wiki import.

## Inputs Required

At least one source:

- manually pasted workout summaries
- files placed under `{athlete-data-root}/raw/imports/`
- optional read-only COROS MCP data if available, such as Codex MCP server
  `coros` at `https://mcpeu.coros.com/mcp`

For each workout, capture whatever is available:

- date
- sport/type
- duration
- distance
- elevation
- pace/HR/power if available
- RPE/subjective notes if available
- tags

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`
- `wiki/workouts/README.md`
- relevant existing `{athlete-data-root}/wiki/workouts/YYYY-MM.md` files
- `{athlete-data-root}/raw/imports/` files when provided
- `.claude/skills/trenere-coros-fetch/SKILL.md` when importing staged COROS data
- `{athlete-data-root}/wiki/log.md`
- `{athlete-data-root}/wiki/meta/last-sync.md`

## Files To Update

- `{athlete-data-root}/wiki/workouts/YYYY-MM.md`
- `wiki/index.md`
- `{athlete-data-root}/wiki/log.md`
- `{athlete-data-root}/wiki/meta/last-sync.md` if an import or sync occurred

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Identify the import source: pasted text, files under `{athlete-data-root}/raw/imports/`, or
   optional COROS MCP.
3. If COROS is requested and available, use it only as a read-only source for
   existing workout data. Do not assume write access or attempt to create
   calendar entries or workouts.
4. If COROS is unavailable or not configured, continue with manual/file input.
5. Parse each workout conservatively. Preserve uncertainty.
6. Create or update the month file `{athlete-data-root}/wiki/workouts/YYYY-MM.md`.
7. Append entries using the format from `wiki/workouts/README.md`.
8. Use `unknown`, `not provided`, or `estimated` for missing or unclear fields.
9. Add tags only when supported by the supplied data.
10. Avoid duplicate entries. If a likely duplicate exists, merge missing fields
   rather than adding a second copy.
11. Update `{athlete-data-root}/wiki/meta/last-sync.md` with source and date.
12. Update `wiki/index.md` current status.
13. Append an `import` or `sync` entry to `{athlete-data-root}/wiki/log.md`.
14. Show changed files before committing if asked to commit.

## Output Format

Return:

- source used
- workouts imported or updated
- files changed
- fields that were missing or estimated
- any injury/fatigue flags noticed
- recommended next skill, usually `/trenere-review`

## Edge Cases

- If dates are missing, ask for dates before writing month files.
- If units are ambiguous, record the original text and mark derived values as
  estimated.
- If the workout appears duplicated, do not create a duplicate.
- If workout data includes medical symptoms, apply the safety boundary from
  `AGENTS.md`.
- If raw files include secrets, auth tokens, or private config, do not commit
  them and warn the athlete.
- If COROS MCP auth expires or tools are unavailable, fall back to pasted
  workouts or files under `{athlete-data-root}/raw/imports/`.

## Git/Log/Index Update Rules

- Append to `{athlete-data-root}/wiki/log.md` using:

```md
## [YYYY-MM-DD] import | Recent workouts imported

Short notes.
```

- Update `wiki/index.md` when recent workout import status changes.
- Update `{athlete-data-root}/wiki/meta/last-sync.md` for any import source, including manual paste.
- Commit only after meaningful updates.
- Default commit:

```bash
git add .
git commit -m "trenere-import: import workouts YYYY-MM-DD"
```
