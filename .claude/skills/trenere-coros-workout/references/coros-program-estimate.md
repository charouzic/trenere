# COROS Program Estimate Reference

Use this reference when building a payload for:

```text
POST https://teameuapi.coros.com/training/program/estimate
```

Important: despite the original assumption, this endpoint estimates a program
but does not attach it to the calendar. To schedule a workout in an active plan,
fetch plan detail and submit an updated future-only plan payload:

```text
GET  https://teameuapi.coros.com/training/plan/detail?id={planId}&supportRestExercise=1
POST https://teameuapi.coros.com/training/schedule/update
```

Header:

```text
accesstoken: $COROS_ACCESS_TOKEN
content-type: application/json
```

## Confirmed Workflow Summary

- Use `COROS_ACCESS_TOKEN` only from the environment; never write it to disk.
- Use `/training/schedule/query` for quick calendar inspection, but use
  `/training/plan/detail` before writes because it includes `entities`.
- `/training/program/estimate` estimates fields; it does not schedule a workout.
- `/training/schedule/update` is the write endpoint for planned workout create,
  update, and delete operations in an active plan.
- Create is safest as one workout per request: send only the new entity, the new
  program, and a complete `versionObjects.status: 1` entry. Including other
  future workouts in the same create payload has been observed to produce messy
  merged/default steps even when COROS returns success.
- Update uses a future-only plan-detail payload plus `versionObjects.status: 2`.
- Move/swap by directly mutating `happenDay`/`dayNo`, or by swapping program
  contents between existing plan entries, has been observed to return `Plan data
  is illegal.` Prefer create-and-verify on the target date, then delete the old
  entry one by one.
- Delete can use a minimal `versionObjects.status: 3` payload, but delete one
  workout per request and verify after each deletion.
- Always refetch plan detail after each write and verify the target date,
  workout name, duration, exercise count, repeat structure, and target ranges.
- Avoid full-plan writes that include past immutable entries; COROS can reject
  them with `17001: Record before today cannot be Operated`.
- If today advances, exclude records before the current date. A payload that was
  valid yesterday can fail today if it still includes yesterday's entity.
- The active plan end date is real operational context. A one-workout create has
  been verified to extend the same plan shell beyond its previous `endDay` when
  the payload sends only the new entity/program, sets `endDay` to the target
  date, updates `totalDay`, increments `maxIdInPlan`/`maxPlanProgramId`, and
  refetch verification confirms the target date. Do not bulk-extend without a
  fresh verification.

## Version Object Status Codes

Observed with `/training/schedule/update`:

| Status | Meaning | Payload style |
| --- | --- | --- |
| `1` | create planned workout | one new entity/program only, plus explicit version object |
| `2` | update planned workout | future-only plan detail with modified entity/program |
| `3` | delete planned workout | minimal `versionObjects` payload is enough; one workout at a time |

For create, include all identifiers in the version object. Omitting
`planProgramId` and `planId` has been observed to fail with `17004: Plan data is
illegal`.

```json
[
  {
    "id": "29",
    "planProgramId": "29",
    "planId": "477504133849596203",
    "status": 1,
    "type": 1
  }
]
```

## Top-Level Shape

```json
{
  "entity": {
    "happenDay": "YYYYMMDD",
    "idInPlan": 17,
    "sortNo": 0,
    "dayNo": 0,
    "sortNoInPlan": 0,
    "sortNoInSchedule": 0
  },
  "program": {
    "access": 1,
    "authorId": "0",
    "id": "0",
    "idInPlan": 17,
    "name": "Workout name",
    "pbVersion": 2,
    "sportType": 1,
    "subType": 65535,
    "type": 0,
    "unit": 0,
    "userId": "0",
    "exercises": []
  }
}
```

For active-plan scheduling, the update payload can use the current plan detail
object as a shell, but for reliable create operations set `entities` and
`programs` to contain only the new planned workout. Do not include immutable
records before today; COROS can reject the update with `17001: Record before
today cannot be Operated`.

For active-plan edits, use the same future-only plan-detail payload, modify the
matching `program` and/or `entity`, and send `status: 2` in `versionObjects`.
This has been verified for changing a planned workout name and overview.

For active-plan deletion, a minimal update payload is enough when the entity
identifiers are known:

```json
{
  "versionObjects": [
    {
      "id": "17",
      "planProgramId": "17",
      "planId": "477504133849596203",
      "status": 3
    }
  ],
  "pbVersion": 2
}
```

Keep unknown static metadata from the observed payload unless a newer known-good
payload proves otherwise. Avoid changing fields such as `sourceId`,
`sourceUrl`, `poolLengthId`, or `pbVersion` without evidence.

## Running Defaults

- `program.sportType`: `1`
- exercise `sportType`: `1`
- equipment: `[1]`
- `hrType`: `3`
- `pbVersion`: `2`
- `subType`: `65535`
- running distance-step values are centimeters (`meters * 100`)
- target duration/rest values are seconds
- pace/HR/power unit interpretation is not fully proven; preserve observed
  values and verify in COROS after creation

## Exercise Types

Observed running payload roles:

- `exerciseType: 1`: warm-up
- `exerciseType: 2`: training/work interval
- `exerciseType: 3`: cool-down
- `exerciseType: 4`: recovery/rest inside a group
- `isGroup: true`: repeat/group container

The observed group pattern is:

1. group container with `isGroup: true`, `id: groupId`, `sets: repeatCount`,
   `restType`, and `restValue`
2. child exercises with `groupId` set to the group container ID
3. child exercises sorted in execution order

## Target Types

Observed values:

- `targetType: 1`, `targetValue: 0`: open/manual step
- `targetType: 2`, `targetValue: 300`: duration target of 300 sec
- `targetType: 5`, `targetValue: 100000`, `targetDisplayUnit: 1`: distance
  target of 1.00 km
- `targetType: 5`, `targetValue: 200000`, `targetDisplayUnit: 1`: distance
  target of 2.00 km
- `restType: 3`, `restValue: 0`: no rest target on normal steps

The running distance scale is centimeters: multiply meters by 100. The same
scale appears in `program.distance`; for example, six 1 km work repetitions
produce `distance: 600000`.

This mapping was corrected and verified from athlete edits on 2026-09-01. The
initial generated 1 km/2 km steps used `targetType: 1` with values `1000` and
`2000`; plan detail retained those numbers, but COROS displayed the work steps
as open/manual and schedule summaries reported no distance. After the athlete
changed the children to `targetType: 5` with `100000`/`200000`, schedule
summaries reported 6.00 km for 6 x 1 km, 8.00 km for 4 x 2 km, and 3.00 km for
3 x 1 km. Do not infer semantics from a plausible-looking plan-detail value.

Treat other target/rest type mappings as unknown until verified from a known-good
payload. If the athlete asks for distance-based or duration-based steps and no
mapping is known, draft the workout and ask for a sample or verify through COROS
before posting.

## Intensity Fields

Observed custom intensity fields:

```json
{
  "intensityCustom": 2,
  "intensityDisplayUnit": 0,
  "intensityMultiplier": 0,
  "intensityPercent": 90500,
  "intensityPercentExtend": 94480,
  "intensityType": 2,
  "intensityValue": 168,
  "intensityValueExtend": 177,
  "isDefaultAdd": 0,
  "isIntensityPercent": true
}
```

Use known athlete anchors conservatively. If encoding HR percentage, preserve the
observed scale where `90500` appears to mean `90.500%`; mark this as inferred.
Do not silently convert pace, HR, or power fields unless the mapping is verified.

## Minimal Builder Rules

- Generate stable numeric `id` values starting at `1`.
- `sortNo` should reflect execution order.
- Use the same `idInPlan` in `entity` and `program`.
- Set `program.name`; do not leave it blank for user-created workouts.
- Set `program.exercises` to include all executable steps and group containers.
- Calculate `program.exerciseNum`, `duration`, `distance`, `targetValue`, and
  `totalSets` only when the mapping is clear; otherwise leave observed-safe
  defaults and rely on API estimation.
- Keep `access: 1` on `program`; keep exercise `access: 0`.

## Active Plan Scheduling Notes

- Fetch plan detail, not only schedule query, because plan detail usually
  includes `entities` needed for update. If plan detail no longer returns
  programs/entities after the visible plan end, recover a recent known-good
  template from `/training/schedule/query?startDate=...&endDate=...`, then use a
  one-workout create with the current plan shell and verify by querying the
  target date.
- Pick `idInPlan = maxIdInPlan + 1` from the latest plan detail immediately
  before each one-by-one create.
- Compute `dayNo` relative to `startDay` where start day is `0`.
- Use `happenDay` for validation and human verification, but COROS mainly uses
  `dayNo`/`idInPlan` in plan updates.
- When copying an existing known-good workout, clear `program.id` and
  `program.planId`, set the new `idInPlan`, and reset chart completion values.
- Add a matching entity with the target `dayNo`, `happenDay`,
  `sortNoInSchedule`, and `exerciseBarChart`.
- For a fully open/manual running step, use `targetType: 1`, `targetValue: 0`,
  and `intensityType: 0`; put the intended duration in the workout name or
  overview if the athlete will stop it manually.
- For no-intensity warm-up, recovery, or cool-down duration steps, use
  `targetType: 2` with seconds plus `intensityType: 0` and zero intensity
  fields.
- For create, send only this new entity/program in the payload arrays. Do not
  include other future entities/programs in the same create request unless a
  later verified workflow proves it safe.
- Send a complete `versionObjects` entry with the new ID, `planProgramId`,
  `planId`, `status: 1`, and `type: 1`, for example:

```json
[
  {
    "id": "17",
    "planProgramId": "17",
    "planId": "477504133849596203",
    "status": 1,
    "type": 1
  }
]
```

- Refetch plan detail after update and verify the target date contains the new
  entity/program with clean exercises. Example clean checks:
  - simple easy run: expected 1-3 exercises, not a duplicated shell
  - grouped strides: expected warm/easy step, one group container, work child,
    recovery child, and cool-down
  - flat fartlek: expected warm-up, repeated work/recovery child steps, and
    cool-down
  - strength/mobility: expected one strength exercise

If the resulting program contains unexpected extra warm-up/main/cool-down steps,
delete that workout immediately and rebuild the payload before continuing.

## Active Plan Deletion Notes

- Fetch plan detail first unless the user supplied all identifiers.
- Identify the target by date/name and resolve `id` from entity `idInPlan`.
- Resolve `planProgramId` from entity `planProgramId`.
- Resolve `planId` from entity `planId`.
- Send `status: 3` in `versionObjects` to mark the planned workout deleted.
- Include `pbVersion: 2` unless current plan detail proves another required
  value.
- Refetch plan detail after deletion and verify the target date/program is gone.
- Do not send the full plan for a simple delete; the minimal version-object
  payload avoids touching unrelated days.
- For multiple deletes, loop one ID at a time and verify absence after each.
  Bulk deletion has been observed to fail with `17004: Plan data is illegal`.

## Active Plan Update Notes

- Fetch plan detail and resolve the target entity/program by date and name.
- Keep only today-and-future editable entities/programs in the update payload.
- Modify the matching program fields, such as `name`, `overview`, steps, or
  targets.
- Do not use update as the default move/swap mechanism. Direct `happenDay`/`dayNo`
  mutation and workout-content swaps have been rejected by COROS with `Plan data
  is illegal.` Use create-verify-delete instead.
- Send `versionObjects` with `status: 2`, for example:

```json
[
  {
    "id": "19",
    "labelId": null,
    "planProgramId": "19",
    "planId": "477504133849596203",
    "status": 2
  }
]
```

- Refetch plan detail after update and verify the changed fields.

## Validation Checklist

- `entity.happenDay` is exactly 8 digits.
- distance-capped running steps use `targetType: 5`, `targetDisplayUnit: 1`,
  and `targetValue = meters * 100`.
- duration-capped steps use `targetType: 2` with `targetValue` in seconds.
- schedule refetch reports aggregate distance equal to repeat count times the
  work-step distance; a missing distance is a failed verification.
- distance-capped repeat groups are not considered fully verified until the
  watch/app shows that the work step is distance-limited, not open/manual.
- all exercise IDs are unique.
- every non-empty `groupId` points to an `isGroup: true` exercise ID.
- every group child appears after its group container.
- target units are explicit in the human summary.
- intensity ranges are explicit and marked as HR, pace, power, RPE, or none.
- no token, cookie, or auth header is written to disk.

## Safe Curl Shape

Use an environment token only:

```sh
curl -sS \
  -H "content-type: application/json" \
  -H "accesstoken: $COROS_ACCESS_TOKEN" \
  -X POST \
  --data @payload.json \
  https://teameuapi.coros.com/training/program/estimate
```

Deletion curl shape:

```sh
curl -sS \
  -H "content-type: application/json" \
  -H "accesstoken: $COROS_ACCESS_TOKEN" \
  -X POST \
  --data '{"versionObjects":[{"id":"17","planProgramId":"17","planId":"477504133849596203","status":3}],"pbVersion":2}' \
  https://teameuapi.coros.com/training/schedule/update
```

Do not paste the token into commands, files, logs, or final answers.
