---
name: obsidian-second-brain
description: >-
  Vin-style Obsidian + agent "second brain": twelve vault slash commands (/context,
  /today, /trace, /connect, /ghost, /challenge, /ideas, /graduate, /closeday, /drift,
  /emerge, /schedule). Set YOUR_OBSIDIAN_VAULT in command files. Honor vault CLAUDE.md
  (keep raw/ immutable unless you override). Prefer Obsidian CLI when available; else rg/Read.
license: MIT
---

# Obsidian second brain (Vin codebook)

## When to use

User says second brain, vault thinking, Internet Vin codebook, or any of the slash names in the map below.

## Constant

- **Vault:** set to **`YOUR_OBSIDIAN_VAULT`** everywhere (absolute path). Example layout: `wiki/`, `raw/`, `wiki/index.md`, `wiki/log.md`, root **`CLAUDE.md`** routing rules.
- **Routing:** Read **`YOUR_OBSIDIAN_VAULT/CLAUDE.md`** before creating or moving wiki pages.

## Tooling

1. **Obsidian CLI** (`obsidian`): only if Obsidian is running and `obsidian help` works. See skill `obsidian-cli` for syntax. Use `vault=` if multiple vaults.
2. **Fallback:** `rg`, `find`, `Read` on the vault path. Never edit **`raw/`** except by explicit user override.

## Slash map (Claude: `~/.claude/commands/` — Cursor: `~/.cursor/commands/`)

| Slash       | File         | Purpose                               |
|------------|--------------|---------------------------------------|
| `/context` | context.md   | Load life/work state from vault      |
| `/today`   | today.md     | Daily plan from notes + tasks         |
| `/trace`   | trace.md     | Idea evolution timeline               |
| `/connect` | connect.md   | Bridge two topics via graph           |
| `/ghost`   | ghost.md     | Answer in your voice from notes       |
| `/challenge` | challenge.md | Contradictions / weak assumptions   |
| `/ideas`   | ideas.md     | Idea report grounded in vault        |
| `/graduate`| graduate.md  | Promote daily scraps to wiki pages    |
| `/closeday`| closeday.md  | End-of-day capture                    |
| `/drift`   | drift.md     | Loose recurring themes                |
| `/emerge`  | emerge.md    | Clusters → projects                   |
| `/schedule`| schedule.md  | Week map from priorities              |

Daily value: richer notes plus these commands so the agent traces **your** graph instead of generic advice.

## Install

1. Copy this file to **`~/.claude/skills/obsidian-second-brain/SKILL.md`** (create the folder).
2. Copy every file from **`commands/`** next to this file into **`~/.claude/commands/`** (or **`~/.cursor/commands/`**).
3. Replace **`YOUR_OBSIDIAN_VAULT`** in each copied command with your real vault path.

## Repo layout

- **`SKILL.md`** (this file) — agent skill body  
- **`commands/*.md`** — one file per slash command  
