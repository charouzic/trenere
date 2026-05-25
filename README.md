# Trenere

Trenere is a local markdown-based endurance coaching wiki.

It is for athletes who want a coding agent to remember training context over
time without turning that memory into an app, database, dashboard, or hosted AI
platform.

The basic loop is:

```text
paste or fetch workouts -> import -> review -> plan -> commit
```

V1 works with only:

- local folders
- markdown files
- a coding agent such as Codex
- pasted workout data
- git

No backend, web app, install step, database, COROS account, GitHub remote, or
Python environment is required.

## What You Get

- A public-safe core repo with agent skills and training knowledge.
- A separate private athlete-data repo for profile, workouts, logs, plans, raw
  imports, injury notes, and personal insights.
- Codex skills for asking questions, onboarding, importing workouts, reviewing
  training, planning weeks, linting the wiki, and optionally fetching COROS data.
- Plain markdown files you can read, edit, diff, and commit.

## What Trenere Is Not

- Not an app.
- Not a backend.
- Not a metrics engine.
- Not a vector database.
- Not COROS automation.
- Not GitHub automation.
- Not a doctor or a medical diagnosis tool.

## Repository Layout

Use two repos:

```text
~/trenere/               public core, safe to publish
~/trenere-athlete-data/  private athlete data, keep private or local-only
```

Public core:

```text
trenere/
  .claude/skills/
  AGENTS.md
  README.md
  wiki/index.md
  wiki/knowledge/
  wiki/workouts/README.md
  templates/athlete-data/
```

Private athlete data:

```text
trenere-athlete-data/
  wiki/profile/
  wiki/log.md
  wiki/workouts/
  wiki/blocks/
  wiki/insights/
  wiki/meta/
  raw/imports/
```

The public repo should not contain athlete-specific data.

## Quick Start

Clone the public core:

```bash
git clone https://github.com/charouzic/trenere.git
cd trenere
```

If you fork Trenere, use your fork URL instead.

Create the private athlete-data repo next to it:

```bash
cp -R templates/athlete-data ../trenere-athlete-data
cd ../trenere-athlete-data
git init
git add .
git commit -m "init: trenere athlete data scaffold"
cd ../trenere
```

Point the public core at the private data repo:

```bash
printf '../trenere-athlete-data\n' > .trenere-athlete-data
```

`.trenere-athlete-data` is ignored by git.

## Install The Codex Skills

Codex reads installed skills from `~/.codex/skills`.

From the public core repo:

```bash
mkdir -p ~/.codex/skills
for d in .claude/skills/trenere-*; do
  name="$(basename "$d")"
  rm -rf "$HOME/.codex/skills/$name"
  cp -R "$d" "$HOME/.codex/skills/$name"
done
```

Restart Codex from the public core repo:

```bash
cd ~/trenere
codex
```

Invoke skills with `$skill-name`, not slash commands:

```text
Use $trenere-ask
```

Codex TUI slash commands like `/help` are built in; custom Trenere skills use
the `$trenere-*` form.

## First Run

Start with the general entrypoint:

```text
Use $trenere-ask. I am setting up Trenere for the first time. What should I do?
```

Then run onboarding:

```text
Use $trenere-onboard for minimal onboarding.
```

Minimal onboarding asks only for:

- current goal
- current weekly volume
- available training days
- fixed constraints
- known zones or rough easy/threshold efforts
- current niggles or injury flags
- recent race/event
- last 2-4 weeks training summary

Unknown is fine. Trenere should not invent athlete-specific facts.

## Daily Or Weekly Use

Ask a general question:

```text
Use $trenere-ask. I slept badly and have intervals planned. What should I do?
```

Import pasted workouts:

```text
Use $trenere-import.

Here are my workouts:
[paste workout summaries]
```

Review recent training:

```text
Use $trenere-review to review the last two weeks.
```

Plan next week:

```text
Use $trenere-plan to plan next week.
```

Health-check the wiki:

```text
Use $trenere-lint.
```

## Optional COROS MCP

Trenere does not require COROS.

If you configure a read-only COROS MCP server in Codex, use:

```text
Use $trenere-coros-fetch to fetch my COROS workouts from the past two weeks and stage them.
Use $trenere-import to import the staged COROS workouts.
```

Keep COROS as read-only for V1. Do not depend on it for the core workflow.

## How The Two Repos Interact

The public core resolves private athlete data in this order:

1. `TRENERE_ATHLETE_DATA` environment variable
2. `.trenere-athlete-data` local pointer file
3. `../trenere-athlete-data`

Example:

```text
Use $trenere-import
```

Reads public files from:

```text
~/trenere/AGENTS.md
~/trenere/wiki/index.md
~/trenere/wiki/workouts/README.md
```

Writes private files to:

```text
~/trenere-athlete-data/wiki/workouts/YYYY-MM.md
~/trenere-athlete-data/wiki/log.md
~/trenere-athlete-data/wiki/meta/last-sync.md
```

## Git Workflow

Commit public framework changes in `trenere`:

```bash
cd ~/trenere
git status --short
git add .
git commit -m "trenere: improve workflow docs YYYY-MM-DD"
git push
```

Commit athlete-specific changes in `trenere-athlete-data`:

```bash
cd ~/trenere-athlete-data
git status --short
git add .
git commit -m "trenere-import: import workouts YYYY-MM-DD"
git push
```

Keep the athlete-data repo private or local-only.

## Safety Boundary

Trenere is not a doctor. If the athlete reports chest pain, unusual breathing
restriction, fever, sharp worsening pain, neurological symptoms, or symptoms
that persist/worsen, stop training and seek medical evaluation.

Do not use Trenere to diagnose injuries or medical conditions.

## Useful Files

- `AGENTS.md` — canonical rules for agents.
- `.claude/skills/trenere-ask/SKILL.md` — general entrypoint.
- `.claude/skills/trenere-onboard/SKILL.md` — athlete profile setup.
- `.claude/skills/trenere-import/SKILL.md` — workout import.
- `.claude/skills/trenere-review/SKILL.md` — recent training review.
- `.claude/skills/trenere-plan/SKILL.md` — next-week planning.
- `.claude/skills/trenere-lint/SKILL.md` — wiki health check.
- `.claude/skills/trenere-coros-fetch/SKILL.md` — optional read-only COROS fetch.
- `templates/athlete-data/` — private athlete-data starter structure.

## Deferred

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
