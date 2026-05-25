---
name: trenere-import
description: Bring recent workouts into the markdown wiki from pasted summaries, raw files, or staged COROS data.
---

# trenere-import

## When To Use

Use when the athlete provides workout summaries, places files under
`{athlete-data-root}/raw/imports/`, or wants staged COROS data imported.

If COROS data still needs to be fetched, use `trenere-coros-fetch` first.

## Inputs Required

At least one source:

- pasted workout summaries
- files under `{athlete-data-root}/raw/imports/`
- staged COROS source material

Capture what is available: date, sport/type, duration, distance, elevation,
pace/HR/power, RPE/notes, and tags.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `wiki/workouts/README.md`
- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`
- existing `{athlete-data-root}/wiki/workouts/YYYY-MM.md`
- provided files under `{athlete-data-root}/raw/imports/`
- `{athlete-data-root}/wiki/log.md`
- `{athlete-data-root}/wiki/meta/last-sync.md`

Resolve `{athlete-data-root}` via `AGENTS.md`.

## Files To Update

- `{athlete-data-root}/wiki/workouts/YYYY-MM.md`
- `{athlete-data-root}/wiki/log.md`
- `{athlete-data-root}/wiki/meta/last-sync.md`

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Identify the source: pasted text, raw import files, or staged COROS data.
3. Parse conservatively and preserve uncertainty.
4. Create or update the monthly workout file.
5. Use the format in `wiki/workouts/README.md`.
6. Use `unknown`, `not provided`, or `estimated` for missing fields.
7. Add tags only when supported by data.
8. Merge likely duplicates instead of creating duplicate entries.
9. Update last-sync and append a compact private log entry.

If COROS records were just fetched, import usable workouts in the same turn
unless the athlete explicitly asked for no writes.

## Output Format

- source used
- workouts imported/updated
- files changed
- missing or estimated fields
- injury/fatigue flags noticed
- next skill, usually `trenere-review`

## Edge Cases

- Missing dates: ask before writing month files.
- Ambiguous units: preserve original text and mark derived values estimated.
- Secrets in raw files: do not commit them; warn the athlete.
- Medical symptoms: apply `AGENTS.md` safety boundary.
- COROS unavailable: continue with pasted or file input.

## Git/Log/Index Update Rules

Log format:

```md
## [YYYY-MM-DD] import | Recent workouts imported

Short notes.
```

Commit only meaningful updates in the private athlete-data repo.
