---
name: dlog
description: "Log and search decisions using the dlog CLI. Use this skill when the user makes a decision (e.g., 'let's use PostgreSQL', 'I'll go with approach B', 'we decided to split the service') or wants to recall/search a past decision (e.g., 'what did we decide about X', 'why did I choose Y'). By default, ASK the user before logging — do not log automatically unless the user has explicitly opted into automatic logging."
---

# dlog — Decision Logger

dlog is a local-first CLI for capturing and searching decisions. Works offline — no login required. Decisions are stored locally in SQLite. You provide all metadata fields when logging.

## No prerequisites needed

dlog works out of the box with no setup. Just run commands directly.

Optional: `dlog login` enables syncing decisions to a remote API for team collaboration.

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
- **--no-enrich**: kept for compatibility (no-op locally). Use when providing metadata fields yourself.
- **--outcome text**: the decision outcome (what was chosen).
- **--reasoning text**: why this decision was made.
- **--alternatives a,b,c**: comma-separated list of alternatives that were considered.
- **--tags x,y,z**: comma-separated tags for categorization.

Keep decision text concise but self-contained — someone reading it months later should understand what was decided without extra context.

**Example:**
```bash
dlog log "Use PostgreSQL over MySQL for the user service — better JSON support and existing PG expertise" -p my-app
```

Output: `Decision logged (id: <uuid>).`

**Example (with metadata):**
```bash
dlog log "Use PostgreSQL over MySQL for the user service" -p my-app --no-enrich \
  --outcome "Chose PostgreSQL" \
  --reasoning "Better JSON support and existing PG expertise" \
  --alternatives "MySQL,SQLite,MongoDB" \
  --tags "database,infrastructure"
```

Output: `Decision logged (id: <uuid>).`

**When called by an LLM:** Always use `--no-enrich` and provide all metadata fields (`--outcome`, `--reasoning`, `--alternatives`, `--tags`). This gives the LLM full control over the metadata.

### Search decisions

```bash
dlog search "query" --limit <n>
```

- **query**: keywords describing what to find (uses full-text search locally)
- **--limit**: max results (default 5, increase only if user asks)

Returns a table: index number, short ID, project, decision summary, date.

### View decision details

```bash
dlog view <id>
```

Use the numeric index from search/list results (e.g., `dlog view 1`) or a full UUID. Shows fields: outcome, reasoning, alternatives, tags.

### List recent decisions

```bash
dlog list --limit <n> --project <name>
```

Browse recent decisions with optional filters.

### Sync (online-only)

```bash
dlog sync
```

Manually sync local decisions with the remote API. Requires `dlog login` first.

Sync also runs automatically in the background when logged in (every 5 minutes).

## Searching for past decisions

When the user wants to recall something they decided before, run a search. Trigger phrases:
- "What did I decide about..."
- "Why did we choose..."
- "Find that decision about..."
- "Did I already decide on..."
- "What was the reasoning for..."

After showing search results, offer to `dlog view <index>` for more details if the user seems interested in a specific result.
