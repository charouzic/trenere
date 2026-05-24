---
name: trenere-import
description: Bring recent workouts into the markdown wiki from pasted summaries, raw files, or optional COROS data.
---

# trenere-import

## When To Use

Use this skill when the athlete provides recent workout summaries, places source
files under `raw/imports/`, or explicitly wants optional COROS-sourced workouts
imported if a COROS MCP is available.

## Inputs Required

At least one source:

- manually pasted workout summaries
- files placed under `raw/imports/`
- optional COROS MCP data if available

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
- `wiki/profile/athlete.md`
- `wiki/profile/coaching-directives.md`
- `wiki/workouts/README.md`
- relevant existing `wiki/workouts/YYYY-MM.md` files
- `raw/imports/` files when provided
- `wiki/log.md`
- `wiki/meta/last-sync.md`

## Files To Update

- `wiki/workouts/YYYY-MM.md`
- `wiki/index.md`
- `wiki/log.md`
- `wiki/meta/last-sync.md` if an import or sync occurred

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Identify the import source: pasted text, files under `raw/imports/`, or
   optional COROS MCP.
3. If COROS is unavailable or not configured, continue with manual/file input.
4. Parse each workout conservatively. Preserve uncertainty.
5. Create or update the month file `wiki/workouts/YYYY-MM.md`.
6. Append entries using the format from `wiki/workouts/README.md`.
7. Use `unknown`, `not provided`, or `estimated` for missing or unclear fields.
8. Add tags only when supported by the supplied data.
9. Avoid duplicate entries. If a likely duplicate exists, merge missing fields
   rather than adding a second copy.
10. Update `wiki/meta/last-sync.md` with source and date.
11. Update `wiki/index.md` current status.
12. Append an `import` or `sync` entry to `wiki/log.md`.
13. Show changed files before committing if asked to commit.

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

## Git/Log/Index Update Rules

- Append to `wiki/log.md` using:

```md
## [YYYY-MM-DD] import | Recent workouts imported

Short notes.
```

- Update `wiki/index.md` when recent workout import status changes.
- Update `wiki/meta/last-sync.md` for any import source, including manual paste.
- Commit only after meaningful updates.
- Default commit:

```bash
git add .
git commit -m "trenere-import: import workouts YYYY-MM-DD"
```
