---
name: trenere-coros-workout
description: Create, schedule, update, or delete structured COROS workouts from Trenere plans using the authenticated COROS web API.
---

# trenere-coros-workout

## When To Use

Use when the athlete asks to create, build, push, publish, schedule, update,
move, remove, or delete a COROS workout from a Trenere plan or from a described
run session. This skill writes to COROS and must be treated differently from
read-only COROS fetch.

Do not use for ordinary planning without a COROS write; use `trenere-plan` first
when the workout itself is not yet decided.

## Agent Decision Rules

- Build the workout from athlete context, recent training, and directives before
  encoding COROS fields.
- Prefer simple run workouts: warm-up, work blocks, recoveries, cool-down.
- Show a human-readable workout summary before posting/deleting unless the user
  already explicitly requested the specific write action.
- If the request is ambiguous, create a draft payload and ask for the missing
  workout-specific detail instead of posting.
- Never store tokens, cookies, auth headers, response bodies with secrets, or
  session data.
- Never post to COROS without an available `COROS_ACCESS_TOKEN`.
- Treat failed `401`/`403` as expired or missing auth; do not retry with stored
  credentials from files.
- Preserve only compact, non-secret notes in Trenere logs when a COROS workout is
  actually created, changed, or deleted.
- Prefer one-by-one writes. Bulk delete/create/update can leave the active plan
  in a messy partial state or be rejected by COROS with generic plan-data
  errors.
- For move/swap requests, prefer create-and-verify on the target date, then
  delete the old entry one by one. Direct date/content swaps can be rejected as
  illegal plan data.
- For creates, verify the resulting exercise list, not only the HTTP/API
  success. COROS can return success while adding or merging unintended default
  steps if the payload shape is wrong.

## Inputs Required

- workout date as `YYYYMMDD`
- workout name
- sport: currently running only unless a reference for another sport is added
- workout steps: target type, target value, intensity type/range, and repeats
- intended action: draft, publish, update, move, or delete

Use wiki context when missing intensity anchors, but mark assumptions.

## Files To Read

- `AGENTS.md`
- `wiki/index.md`
- `wiki/coach-knowledge.md`
- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/preferences.md`
- `{athlete-data-root}/wiki/profile/injury-history.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`
- recent `{athlete-data-root}/wiki/workouts/YYYY-MM.md`
- recent `{athlete-data-root}/wiki/log.md`
- `.claude/skills/trenere-plan/SKILL.md` when deriving the workout from a plan
- `.claude/skills/trenere-coros-fetch/SKILL.md` when inspecting existing COROS
  schedules or planned-workout formats

## Files To Update

- `{athlete-data-root}/wiki/log.md` only after successful COROS write

Do not write raw COROS request or response dumps. If debugging is necessary,
stage a redacted, compact note only after removing auth headers, cookies, and
irrelevant private data.

## COROS API

Read `references/coros-program-estimate.md` before building a create, update, or
delete payload.

Observed estimate endpoint:

```text
POST https://teameuapi.coros.com/training/program/estimate
Header: accesstoken: $COROS_ACCESS_TOKEN
Body: JSON
```

This endpoint returns calculated fields such as distance, duration, and training
load, but it does not attach a workout to the calendar by itself.

Observed scheduling flow for an active plan:

```text
GET  https://teameuapi.coros.com/training/plan/detail?id={planId}&supportRestExercise=1
POST https://teameuapi.coros.com/training/schedule/update
```

Observed active-plan deletion flow:

```text
POST https://teameuapi.coros.com/training/schedule/update
Body: {"versionObjects":[{"id":"17","planProgramId":"17","planId":"477504133849596203","status":3}],"pbVersion":2}
```

Use `COROS_ACCESS_TOKEN` from the environment only. If it is missing, return a
draft payload and clear curl command shape with `$COROS_ACCESS_TOKEN`, but do not
invent or persist credentials.

## Steps

1. Follow the session start routine in `AGENTS.md`.
2. Confirm the intended COROS write action: create, update, move, or delete.
3. Review recent training, recovery risk, and directives if the workout still
   needs coaching judgment.
4. Build a plain-language action summary first.
5. Convert the workout into COROS program payload fields using the reference.
6. Validate date format, step ordering, group/repeat IDs, targets, and intensity
   ranges.
7. If not explicitly told to publish now, stop with the summary and draft
   payload and ask for confirmation.
8. If publishing into an active plan, fetch the full plan detail, create one
   workout at a time, and POST only the new entity/program plus the required
   create `versionObjects` entry to `/training/schedule/update`.
9. For move/swap requests, create and verify the replacement workout on the
   target date before deleting the old entry; avoid direct `happenDay`/`dayNo`
   mutation unless a newer verified workflow proves it safe.
10. If deleting from an active plan, resolve the existing entity/program from
   plan detail and POST one minimal `versionObjects` deletion payload per
   workout.
11. Verify each write by refetching plan detail for the target date and checking
   name, duration, step count, repeat structure, and target ranges before
   continuing.
12. Report success/failure with status code and non-secret response summary.
13. On success, append a compact private log entry.

## Output Format

- workout summary
- target date
- COROS action: `drafted`, `posted`, `updated`, `deleted`, or `blocked`
- payload path or inline compact payload when useful
- validation notes
- auth/API limitation, if any
- log file updated, if any

## Edge Cases

- Missing token: draft only.
- Ambiguous date or workout structure: draft only and ask one concise follow-up.
- Existing scheduled workout on that date: inspect schedule or plan detail; ask
  before overwriting, duplicating, or deleting unless the target is explicit.
- Replacing a messy schedule: delete existing target workouts one by one and
  verify each deletion before creating replacements one by one.
- Active plan end date: if `plan.detail.endDay` is earlier than the requested
  workout date, do not assume creates beyond the plan end will work; report that
  the plan must be extended or a new plan created unless a verified extension
  workflow exists.
- Medical or injury flags: apply `AGENTS.md` safety boundary before writing.
- Non-running workout: draft in plain language unless a COROS field reference for
  that sport is available.

## Git/Log/Index Update Rules

Log format:

```md
## [YYYY-MM-DD] coros-workout | COROS workout changed

Short notes: action, date, workout name, purpose, and any uncertainty. No
tokens, headers, raw API dumps, or private response blobs.
```

Commit only meaningful public skill updates. Do not commit private athlete data
unless the user explicitly asks.
