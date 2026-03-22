# dlog-skill

A [Claude Code skill](https://docs.anthropic.com/en/docs/claude-code/skills) for logging and searching decisions using the [dlog](https://github.com/tonytangdev/log-decisions-simplified) CLI.

## Install

```bash
claude skill install tonytangdev/dlog-skill
```

## What it does

- Detects decisions during conversations and offers to log them
- Searches and recalls past decisions
- Integrates with dlog teams and projects

## Usage

Once installed, the skill activates when you make or reference decisions in Claude Code. By default it asks before logging — say "log decisions automatically" to skip confirmations.
