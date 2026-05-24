# Trenere Log

Use this chronological format:

```md
## [YYYY-MM-DD] {type} | {title}

Short notes.
```

Allowed types: `onboard`, `import`, `review`, `plan`, `lint`, `insight`,
`sync`.

## [2026-05-24] onboard | V1 scaffold created

Created the local markdown coaching wiki scaffold. Athlete-specific facts are
not populated yet; run `/trenere-onboard` before making substantive coaching
recommendations.

## [2026-05-24] sync | COROS MCP configured

Configured Codex global MCP server `coros` at `https://mcpeu.coros.com/mcp` with
OAuth. COROS remains optional and read-only for Trenere V1; manual pasted
workouts and files under `raw/imports/` remain the baseline import path.
