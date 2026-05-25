# Trenere

Trenere is a local markdown-based endurance coaching wiki.

It is not an app, backend, metrics engine, or AI coaching platform. It is a small
set of markdown files and Codex skills that help maintain training memory over
time.

Core loop:

```text
import workouts -> review training -> plan next week -> commit changes
```

## Setup

Use two repos: public core plus private athlete data.

```bash
git clone https://github.com/charouzic/trenere.git
cd trenere

cp -R templates/athlete-data ../trenere-athlete-data
cd ../trenere-athlete-data
git init
git add .
git commit -m "init: trenere athlete data scaffold"

cd ../trenere
printf '../trenere-athlete-data\n' > .trenere-athlete-data
```

`.trenere-athlete-data` is ignored by git. Keep `trenere-athlete-data` private
or local-only.

## Install Codex Skills

```bash
cd ~/trenere
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

Invoke skills with `$`, not `/`:

```text
Use $trenere-ask
```

## Use

Start here:

```text
Use $trenere-ask. I am setting up Trenere for the first time.
```

Main skills:

- `$trenere-ask` — general entrypoint when unsure what to do
- `$trenere-onboard` — create or update athlete profile
- `$trenere-import` — import pasted or staged workouts
- `$trenere-review` — review recent training
- `$trenere-plan` — plan next week
- `$trenere-lint` — health-check the wiki
- `$trenere-coros-fetch` — optional read-only COROS fetch and staging

Typical flow:

```text
Use $trenere-onboard for minimal onboarding.
Use $trenere-import. Here are my workouts: ...
Use $trenere-review to review the last two weeks.
Use $trenere-plan to plan next week.
```

## Data Split

Public core repo:

```text
trenere/
  .claude/skills/
  AGENTS.md
  wiki/knowledge/
  templates/athlete-data/
```

Private athlete-data repo:

```text
trenere-athlete-data/
  wiki/profile/
  wiki/log.md
  wiki/workouts/
  wiki/insights/
  raw/imports/
```

Trenere resolves private data from:

1. `TRENERE_ATHLETE_DATA`
2. `.trenere-athlete-data`
3. `../trenere-athlete-data`

## Notes

- Read `AGENTS.md` for the canonical agent rules.
- Keep athlete-specific data out of the public core repo.
- COROS is optional and read-only in V1.
- Trenere is not a doctor. For chest pain, unusual breathing restriction, fever,
  sharp worsening pain, neurological symptoms, or symptoms that persist/worsen,
  stop training and seek medical evaluation.
