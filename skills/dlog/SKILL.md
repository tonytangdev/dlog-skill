---
name: dlog
description: "Log and search decisions using the dlog CLI. Use this skill when the user makes a decision (e.g., 'let's use PostgreSQL', 'I'll go with approach B', 'we decided to split the service') or wants to recall/search a past decision (e.g., 'what did we decide about X', 'why did I choose Y'). By default, ASK the user before logging — do not log automatically unless the user has explicitly opted into automatic logging."
---

# dlog — Decision Logger

dlog is a CLI tool for quickly capturing and searching decisions. It takes seconds to log a decision. After logging, an LLM enrichment process automatically adds outcome, reasoning, alternatives, and tags.

## Prerequisites — check before any command

Before running any dlog command, verify the user is set up:

1. **Logged in?** Run `dlog team list` — if it errors with "Not logged in", tell the user to run `dlog login` (this is interactive and requires their input — you cannot do it for them).
2. **Has a team?** If `dlog team list` returns no teams, tell the user to run `dlog team create` or `dlog team join <code>`.
3. **Active team set?** If the user wants to log to a specific team, they can use `--team <slug>` on any command or switch with `dlog use <slug>`.

Only proceed with log/search commands once auth and team are confirmed.

## Logging behavior

**Default: ask before logging.** When you detect a decision, ask: **"Want me to log this decision with dlog?"** — then run the command only after they confirm. One offer per decision is enough. If the user declines, move on. **Never log automatically unless the user has explicitly said to do so** (e.g., "log decisions automatically", "don't ask, just log them").

Decisions look like:

- Choosing one option over another ("let's go with X instead of Y")
- Settling on an approach ("I'll use the factory pattern here")
- Trade-off resolutions ("we'll accept the latency hit for simpler code")
- Technology/tool choices ("switching from Jest to Vitest")
- Architecture calls ("the API will be REST, not GraphQL")

## Commands

### Log a decision

```bash
dlog log "decision text" -p <project>
```

- **decision text**: concise summary of what was decided. Write it as a clear statement, not a question. Include the "why" if mentioned.
- **-p project**: infer the project name from context — the current repo name, the project being discussed, or ask if unclear.
- **--team slug**: optional, override the active team for this command.

Keep decision text concise but self-contained — someone reading it months later should understand what was decided without extra context.

**Example:**
```bash
dlog log "Use PostgreSQL over MySQL for the user service — better JSON support and existing PG expertise" -p my-app
```

Output: `Decision logged (id: <uuid>). Enrichment in progress.`

### Search decisions

```bash
dlog search "query" --limit <n>
```

- **query**: keywords or natural language describing what to find
- **--limit**: max results (default 5, increase only if user asks)

Returns a table: index number, short ID, project, decision summary, date.

### View decision details

```bash
dlog view <id>
```

Use the numeric index from search/list results (e.g., `dlog view 1`) or a full UUID. Shows enriched fields: outcome, reasoning, alternatives, tags.

### List recent decisions

```bash
dlog list --limit <n> --project <name>
```

Browse recent decisions with optional filters.

## Searching for past decisions

When the user wants to recall something they decided before, run a search. Trigger phrases:
- "What did I decide about..."
- "Why did we choose..."
- "Find that decision about..."
- "Did I already decide on..."
- "What was the reasoning for..."

After showing search results, offer to `dlog view <index>` for more details if the user seems interested in a specific result.
