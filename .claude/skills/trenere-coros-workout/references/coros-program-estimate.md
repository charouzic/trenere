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

For active-plan scheduling, the update payload is the current plan detail object
with added `entities`, `programs`, and `versionObjects` for the new workout. Do
not include immutable records before today; COROS can reject the update with
`17001: Record before today cannot be Operated`.

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
- target distance values are meters
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

- `targetType: 2`, `targetValue: 300`: distance target of 300 m
- `restType: 3`, `restValue: 0`: no rest target on normal steps

Treat other target/rest type mappings as unknown until verified from a known-good
payload. If the athlete asks for duration-based steps and no mapping is known,
draft the workout and ask for a sample or verify through COROS before posting.

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

- Fetch plan detail, not only schedule query, because plan detail includes
  `entities` needed for update.
- Pick `idInPlan = maxIdInPlan + 1`.
- Compute `dayNo` relative to `startDay` where start day is `0`.
- Use `happenDay` for validation and human verification, but COROS mainly uses
  `dayNo`/`idInPlan` in plan updates.
- When copying an existing known-good workout, clear `program.id` and
  `program.planId`, set the new `idInPlan`, and reset chart completion values.
- Add a matching entity with the target `dayNo`, `happenDay`,
  `sortNoInSchedule`, and `exerciseBarChart`.
- Send `versionObjects` with the new ID, for example:

```json
[{ "id": "17", "status": 1, "type": 1 }]
```

- Refetch plan detail after update and verify the target date contains the new
  entity/program.

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

## Validation Checklist

- `entity.happenDay` is exactly 8 digits.
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
