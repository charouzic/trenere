# Trenere

Trenere is a local markdown-based endurance coaching wiki.

It is not an app, backend, metrics engine, or AI coaching platform. It is a
small repo of markdown files and agent workflows that help preserve coaching
memory over time.

## Setup

Use a public core repo plus private athlete data:

```bash
git clone https://github.com/charouzic/trenere.git
cd trenere
cp -R templates/athlete-data ../trenere-athlete-data
cd ../trenere-athlete-data
git init && git add . && git commit -m "init: trenere athlete data scaffold"
cd ../trenere
printf '../trenere-athlete-data\n' > .trenere-athlete-data
```

Keep `trenere-athlete-data` private or local-only.

## Agent Use

Trenere is agent-agnostic. Skills live in `.claude/skills/` as readable workflow
files.

Claude Code can use them from the repo. For Codex, copy them into
`~/.codex/skills`:

```bash
mkdir -p ~/.codex/skills
for d in .claude/skills/trenere-*; do
  name="$(basename "$d")"
  rm -rf "$HOME/.codex/skills/$name"
  cp -R "$d" "$HOME/.codex/skills/$name"
done
```

Codex uses `$trenere-ask`, not `/trenere-ask`.

## Flow

```text
trenere-onboard -> trenere-import -> trenere-review -> trenere-plan
```

Use `trenere-ask` when unsure. Use `trenere-lint` for wiki health. Use
`trenere-coros-fetch` only if COROS MCP is configured; V1 works without it.

## COROS Access Token For Agents

Basic Trenere use does not require COROS. Agents should treat COROS as optional
and read-only unless a specific skill says otherwise. COROS MCP can fetch
workout records, but some useful COROS data is only available through the web
API. The `trenere-coros-workout` skill includes a macOS Keychain-backed
`coros-auth` wrapper that stores only a password-derived login hash and can
obtain a memory-only token automatically; an inherited `COROS_ACCESS_TOKEN`
remains a fallback.

Agent rule: when the task involves planned-vs-actual workout review, planned
blocks, target ranges, schedule inspection, or future workout-building features,
check whether `COROS_ACCESS_TOKEN` or the Keychain-backed wrapper is available
before concluding that COROS does not expose the needed data. The authenticated
web API can expose planned workout blocks, target ranges, and schedule data that
may not be present in MCP summaries or exported FIT files.

If the token is missing or returns `401`/`403` or COROS result `1019`, use the
Keychain-backed wrapper once when configured. Otherwise continue with MCP,
staged files, or pasted data and state the limitation. Never commit token
values, cookies, auth headers, credentials, or session data.

## Public vs Private

Public core:

- `.claude/skills/`
- `AGENTS.md`
- `wiki/index.md`
- `wiki/coach-knowledge.md`
- `wiki/workouts/README.md`
- `templates/athlete-data/`

Private athlete data:

- `wiki/profile/`
- `wiki/log.md`
- `wiki/workouts/`
- `wiki/insights/`
- `raw/imports/`

Resolve private data from `TRENERE_ATHLETE_DATA`, `.trenere-athlete-data`, then
`../trenere-athlete-data`.

## Boundaries

Keep athlete-specific data out of the public core. Do not commit secrets or auth
tokens. Trenere is not a doctor; for chest pain, unusual breathing restriction,
fever, sharp worsening pain, neurological symptoms, or persistent/worsening
symptoms, stop training and seek medical evaluation.
