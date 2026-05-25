# Trenere

Trenere is a local markdown-based endurance coaching wiki.

It is not an app, backend, or AI coaching platform. It is a small repo of
markdown files and agent workflows that help maintain coaching memory over time.

V1 works with only a local folder, markdown files, a coding agent, pasted workout
data, and git. No external service, install step, COROS access, GitHub remote, or
Python environment is required.

This repository is intended to be public-safe. Athlete-specific data should live
in a separate private/local repo, by default:

```text
../trenere-athlete-data
```

## What Trenere Is

- A single-athlete coaching memory stored in markdown.
- A lightweight set of agent skills for onboarding, workout import, review,
  planning, and wiki linting.
- A local git repo so meaningful changes can be reviewed and committed.
- A public core that can be shared without athlete-specific history.

## What Trenere Is Not

- Not a custom MCP server.
- Not a backend, database, web app, Streamlit app, or metrics engine.
- Not COROS automation or GitHub automation.
- Not a medical tool, diagnosis system, or replacement for professional care.

## Start Here

1. Open `AGENTS.md`.
2. Read `wiki/index.md`.
3. Create or connect a private athlete-data repo from `templates/athlete-data/`.
4. Use `$trenere-ask` if you are unsure what to do next, or run
   `/trenere-onboard` to create or update the athlete profile.

## Public/Private Layout

Public core repo:

```text
trenere/
  .claude/skills/
  wiki/index.md
  wiki/knowledge/
  wiki/workouts/README.md
  templates/athlete-data/
```

Private athlete-data repo:

```text
trenere-athlete-data/
  wiki/profile/
  wiki/log.md
  wiki/workouts/YYYY-MM.md
  wiki/blocks/
  wiki/insights/
  wiki/meta/
  raw/imports/
```

Agents resolve private data in this order:

1. `TRENERE_ATHLETE_DATA`
2. ignored local pointer file `.trenere-athlete-data`
3. sibling repo `../trenere-athlete-data`

Do not commit athlete-specific data to the public core.

## Ask Trenere

Use `/trenere-ask` as the general entrypoint when you are unsure, need help,
want a training-load adjustment, or do not know which workflow to run.

In Codex, invoke it as:

```text
Use $trenere-ask
```

Examples:

```text
Use $trenere-ask. I slept badly and have intervals planned. What should I do?
Use $trenere-ask. I am not sure whether to import, review, or plan next.
Use $trenere-ask. Does my current week look too hard?
```

## Run Onboarding

Use `/trenere-onboard`.

Minimal onboarding is enough for V1. Provide:

- current goal
- current weekly volume
- available training days
- fixed constraints
- known zones or rough easy/threshold efforts
- current niggles/injury flags
- recent race/event
- last 2-4 weeks training summary

Deep onboarding can happen later when there is more context.

## Import or Paste Workouts

Use `/trenere-import`.

Supported V1 sources:

- manually pasted workout summaries
- files placed under `{athlete-data-root}/raw/imports/`
- optional read-only COROS MCP if available

If COROS is unavailable, continue with manual or file input. Workout entries live
under the private athlete-data repo at `{athlete-data-root}/wiki/workouts/YYYY-MM.md`.

## Fetch From COROS

Use `/trenere-coros-fetch` when you want to get read-only data from COROS MCP
before importing, reviewing, or planning.

Typical flow:

```text
Use $trenere-coros-fetch to fetch my COROS workouts from the past two weeks and stage them.
Use $trenere-import to import the staged COROS workouts.
Use $trenere-review to review recent training.
```

If COROS MCP is available in Codex, use it only as a read-only source for
existing training data.

Optional COROS MCP local setup:

```text
Suggested Codex MCP name: coros
URL: choose the COROS regional MCP endpoint for the athlete
Auth: OAuth, configured locally
Mode: read-only import/analysis source
```

## Weekly Review

Use `/trenere-review`.

The review should summarize what happened, easy/hard balance, what went well,
risk signals, what should affect next week, and whether any durable insight
belongs in `{athlete-data-root}/wiki/insights/`.

## Plan Next Week

Use `/trenere-plan`.

Plans should include a weekly goal, day-by-day schedule, session purpose,
intensity guidance, fallback rules, and feedback to report after key sessions.

## Commit Changes

Use local git after meaningful changes:

```bash
git status --short
git add .
git commit -m "{skill}: {one-line summary} YYYY-MM-DD"
```

Do not push unless explicitly requested. Do not commit secrets, auth tokens,
private config, or accidental private dumps.

Commit public workflow/template changes in `trenere`. Commit athlete-specific
profile, workout, log, plan, insight, and raw import changes in
`trenere-athlete-data`.

## Deferred to V2

- custom MCP server
- backend or database
- web app or Streamlit app
- vector search
- metrics engine
- COROS write automation
- GitHub automation
- nutrition module
- automated workout upload
- Python package setup
