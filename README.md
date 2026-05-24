# Trenere

Trenere is a local markdown-based endurance coaching wiki.

It is not an app, backend, or AI coaching platform. It is a small repo of
markdown files and agent workflows that help maintain coaching memory over time.

V1 works with only a local folder, markdown files, a coding agent, pasted workout
data, and git. No external service, install step, COROS access, GitHub remote, or
Python environment is required.

## What Trenere Is

- A single-athlete coaching memory stored in markdown.
- A lightweight set of agent skills for onboarding, workout import, review,
  planning, and wiki linting.
- A local git repo so meaningful changes can be reviewed and committed.

## What Trenere Is Not

- Not a custom MCP server.
- Not a backend, database, web app, Streamlit app, or metrics engine.
- Not COROS automation or GitHub automation.
- Not a medical tool, diagnosis system, or replacement for professional care.

## Start Here

1. Open `AGENTS.md`.
2. Read `wiki/index.md`.
3. Run `/trenere-onboard` to create or update the athlete profile.

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
- files placed under `raw/imports/`
- optional COROS MCP if available

If COROS is unavailable, continue with manual or file input. Workout entries live
under `wiki/workouts/YYYY-MM.md`.

## Weekly Review

Use `/trenere-review`.

The review should summarize what happened, easy/hard balance, what went well,
risk signals, what should affect next week, and whether any durable insight
belongs in `wiki/insights/`.

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

## Deferred to V2

- custom MCP server
- backend or database
- web app or Streamlit app
- vector search
- metrics engine
- COROS automation
- GitHub automation
- nutrition module
- automated workout upload
- Python package setup
