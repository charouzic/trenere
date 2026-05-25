---
name: trenere-ask
description: General Trenere entrypoint for questions, uncertainty, training-load adjustments, coaching advice, and routing to the right Trenere workflow.
---

# trenere-ask

## Athlete Data Root

Before reading or writing private coaching data, resolve `{athlete-data-root}` using `AGENTS.md`: `TRENERE_ATHLETE_DATA`, then `.trenere-athlete-data`, then `../trenere-athlete-data`. Public core files are read from this repository; athlete-specific profile, logs, workouts, imports, blocks, insights, and sync notes are read or written under `{athlete-data-root}`. Commit public core changes in this repo and private athlete-data changes from `{athlete-data-root}`.


## When To Use

Use this skill when the athlete is unsure what to do, asks a general coaching
question, wants help interpreting training context, needs a training-load
adjustment, or does not know whether to run import, review, plan, lint, or COROS
fetch. Also use it when the athlete asks whether Trenere learned anything new or
should improve itself from the recent conversation.

This is the front door into Trenere. It should gather enough context to be useful
without forcing the athlete to choose the perfect workflow first.

## Inputs Required

Use the athlete's question as the primary input.

Ask at most 1-3 short follow-up questions only when the answer cannot be safely
derived from the wiki and the missing detail changes the recommendation.

Common useful inputs:

- what decision the athlete is trying to make
- current fatigue, soreness, illness, sleep, or stress status
- target date or training week
- recent workout context, if not already imported
- whether the athlete wants advice only or wants the wiki updated

If the request causes the agent to retrieve or view concrete workout records
from an external source such as COROS, do not leave those records only in the
chat transcript. Stage and/or import them unless the athlete explicitly asks for
a no-write summary.

## Files To Read

Always read:

- `AGENTS.md`
- `wiki/index.md`
- `{athlete-data-root}/wiki/profile/athlete.md`
- `{athlete-data-root}/wiki/profile/coaching-directives.md`

Read as needed:

- `{athlete-data-root}/wiki/profile/injury-history.md`
- `{athlete-data-root}/wiki/profile/preferences.md`
- `{athlete-data-root}/wiki/log.md`
- recent `{athlete-data-root}/wiki/workouts/YYYY-MM.md`
- `{athlete-data-root}/wiki/meta/last-sync.md`
- relevant skill files under `.claude/skills/trenere-*/SKILL.md`
- `{athlete-data-root}/wiki/insights/` pages
- public knowledge pages from the routing table below

## Public Knowledge Routing

Read only the pages relevant to the question:

- General training philosophy or conservative defaults:
  `wiki/principles/training_principles.md`.
- Easy/hard balance, gray-zone concerns, or workout load:
  `wiki/principles/intensity_distribution.md`.
- Volume changes, mileage increases, or long-run progression:
  `wiki/principles/volume_and_mileage.md` and
  `wiki/programming/progression_rules.md`.
- Fatigue, poor sleep, HRV, resting HR, stress, or whether to reduce training:
  `wiki/principles/recovery_and_adaptation.md` and
  `wiki/programming/deload_rules.md`.
- Pain, niggles, injury return, or gait changes:
  `wiki/principles/injury_prevention.md` and
  `wiki/programming/return_from_niggle_rules.md`.
- Strength training:
  `wiki/principles/strength_training.md`.
- Next-week planning:
  `wiki/programming/weekly_structure_examples.md`,
  `wiki/programming/progression_rules.md`, and the relevant runner profile.
- Goal-race planning:
  relevant pages under `wiki/races/` plus
  `wiki/principles/periodization.md`.
- Specific workout advice:
  relevant pages under `wiki/workouts/`.
- COROS, FIT, HR, pace, power, GPS, elevation, or planned-vs-actual analysis:
  `wiki/data/training_data_interpretation.md`.
- Evidence strength or uncertain claims:
  `wiki/evidence/evidence_summary.md` and
  `wiki/evidence/weak_or_uncertain_claims.md`.

## Files To Update

Usually none.

Update files only when the athlete asks for a change or the answer naturally
creates useful coaching memory:

- `{athlete-data-root}/wiki/log.md` for meaningful advice, decisions, or handoffs
- `{athlete-data-root}/wiki/profile/coaching-directives.md` for durable planning rules
- `{athlete-data-root}/wiki/profile/injury-history.md` for athlete-reported injury flags
- `{athlete-data-root}/raw/imports/` when external source records were fetched
  and should be preserved
- `{athlete-data-root}/wiki/workouts/YYYY-MM.md` when fetched workout records
  include enough information to create robust workout entries
- `wiki/index.md` if current status changes
- other wiki pages only when a specific routed workflow requires them

## Decision Flow

1. Follow the session start routine in `AGENTS.md`.
2. Classify the request:
   - answer: explain or advise without changing files
   - triage: identify the right next skill or missing context
   - adjust: modify a planned or implied training load conservatively
   - route: hand off to a specific skill
   - update: write durable coaching memory
   - learn: audit recent work for durable athlete-specific or general-agent
     improvements
3. Check safety boundaries before giving training advice.
4. Use existing wiki context before asking questions.
5. If context is missing but the risk is low, state assumptions and give a
   conservative answer.
6. If context is missing and the risk is meaningful, ask only the minimum
   follow-up questions.
7. If a specific workflow is clearly needed, say which skill should run next and
   why.
8. If the athlete wants you to proceed, follow the relevant skill file.

## Self-Improvement Flow

Use this flow when the athlete asks whether Trenere learned anything new, asks
the agent to improve itself, or asks to capture learnings from recent work.

1. Read the self-improvement rules in `AGENTS.md`.
2. Review recent conversation context, current git diffs, and recent log/sync
   entries relevant to the request.
3. Classify each candidate learning into one of three outcomes:
   - private athlete learning
   - public general agent learning
   - no durable update
4. For private athlete learning, update only `{athlete-data-root}` files:
   - `{athlete-data-root}/wiki/profile/coaching-directives.md` for standing
     planning rules
   - `{athlete-data-root}/wiki/profile/preferences.md` for athlete preferences
   - `{athlete-data-root}/wiki/profile/injury-history.md` for athlete-reported
     injury flags
   - `{athlete-data-root}/wiki/insights/` for durable athlete-specific training
     conclusions
   - `{athlete-data-root}/wiki/log.md` for minor or provisional learnings
5. For public general agent learning, update only public core files:
   - `AGENTS.md` for canonical behavior
   - `.claude/skills/trenere-*/SKILL.md` for workflow-specific instructions
   - `README.md` for user-facing setup or usage
   - structured public wiki knowledge pages for general training knowledge
6. Do not create public docs from private athlete facts. Generalize only when
   the lesson is reusable without exposing the athlete.
7. If public skills are changed, sync the installed Codex skill copies under
   `~/.codex/skills/trenere-*` when available.
8. Show changed files for each affected repo.
9. Commit and push each affected repo separately to `master` when the request
   explicitly asks for self-improvement persistence. Do not push unrelated
   changes.

Commit messages:

```bash
# public core
git add .
git commit -m "trenere-ask: capture agent learning YYYY-MM-DD"
git push origin master

# private athlete-data
git add .
git commit -m "trenere-ask: capture athlete learning YYYY-MM-DD"
git push origin master
```

## Memory Persistence Rules

- Ordinary Q&A can remain answer-only.
- If the agent fetches, sees, or summarizes actual workout records from COROS or
  another source, preserve them in the private athlete-data repo in the same
  turn unless the athlete explicitly requested no file writes.
- For fetched workout records, prefer this order:
  1. Stage source material under `{athlete-data-root}/raw/imports/`.
  2. Import usable workout entries into
     `{athlete-data-root}/wiki/workouts/YYYY-MM.md`.
  3. Update `{athlete-data-root}/wiki/meta/last-sync.md`.
  4. Append a compact entry to `{athlete-data-root}/wiki/log.md`.
- If only a partial summary is available, stage the summary and mark missing
  fields as `not provided`; do not invent precision.
- If enough fields exist for a workout entry, do not stop at "I can see them";
  write them into the workout wiki or run `trenere-import` in the same turn.

## Routing Rules

- Use `/trenere-onboard` when profile basics are missing and the athlete wants
  coaching beyond generic advice.
- Use `/trenere-coros-fetch` when the athlete asks to get data from COROS.
- Use `/trenere-import` when the athlete provides workouts or staged source
  material to add to the wiki.
- Use `/trenere-review` when the athlete asks what recent training means.
- Use `/trenere-plan` when the athlete asks what to do next week or wants a
  schedule.
- Use `/trenere-lint` when the athlete asks whether the wiki is consistent,
  stale, or messy.

Do not force a route when a direct answer is enough.

## Training-Load Adjustment Rules

When the athlete asks "what should I do?" or "should I adjust?", use these
defaults:

- If sleep, stress, soreness, illness, or injury flags are poor, reduce load
  before adding intensity.
- If soreness is mild and improving, keep easy work easy and avoid hero sessions.
- If fatigue is high or motivation is unusually low, shorten the session or
  replace quality with easy aerobic work.
- If there is sharp pain, worsening pain, fever, chest pain, unusual breathing
  restriction, neurological symptoms, or symptoms that persist/worsen, advise
  stopping training and seeking medical evaluation.
- Do not diagnose injuries or medical conditions.
- Do not overfit to one workout.
- Preserve consistency when possible, but choose rest when training would be
  forced or risky.

## Output Format

Keep the answer short and useful.

Use this shape when practical:

```md
Short answer: [1-3 sentences]

Why: [2-4 bullets grounded in the wiki or stated assumptions]

Do now:
- [action]
- [fallback if tired/sore/stressed]

Next Trenere step: [skill or "none"]
```

If asking follow-up questions, ask no more than 3 and explain why the answers
matter in one sentence.

If changing files, include:

- files changed
- whether a commit was made
- next useful skill

## Edge Cases

- If onboarding has not happened, answer cautiously and recommend
  `/trenere-onboard` before specific planning.
- If no recent workouts are imported, recommend `/trenere-coros-fetch` or
  `/trenere-import` before drawing strong conclusions.
- If the athlete asks for a medical diagnosis, decline to diagnose and suggest
  appropriate medical evaluation when symptoms warrant it.
- If the athlete asks for an aggressive plan without supporting context, explain
  the uncertainty and choose the conservative option.
- If the athlete asks for broad life, nutrition, or medical advice outside
  Trenere's scope, keep the answer limited and route back to training context.
- If the athlete wants a file update but the change is not durable, prefer a
  short `{athlete-data-root}/wiki/log.md` note instead of creating a new page.
- If the athlete asks whether Trenere learned anything new and there is no
  durable lesson, say so and do not create filler updates.
- If a durable lesson affects both the athlete and the agent, split it into two
  separate commits in the appropriate repos.
- If either repo has unrelated local changes, do not include them in a
  self-improvement commit; report the dirty files and continue with the clean
  repo if possible.

## Git/Log/Index Update Rules

- Do not update files for ordinary Q&A.
- Do update files when a supposedly "ask" request retrieves concrete workout
  records or other coaching memory that would otherwise be lost after the chat.
- Append to `{athlete-data-root}/wiki/log.md` only for meaningful coaching decisions, load
  adjustments, or workflow handoffs that should be remembered.
- If appending to `{athlete-data-root}/wiki/log.md`, use:

```md
## [YYYY-MM-DD] review | Coaching question answered

- Question: short summary.
- Decision: short summary.
- Next: short follow-up.
```

- Use `review` for advice/interpretation, `plan` for schedule changes, `lint`
  for wiki-health questions, and `sync` for data-source handoffs.
- Commit only after meaningful wiki updates.
- For explicit self-improvement requests, commit and push meaningful public and
  private updates separately to `master`.
- Default commit:

```bash
git add .
git commit -m "trenere-ask: update coaching memory YYYY-MM-DD"
```
