---
name: obsidian-second-brain
description: >-
  Vin-style Obsidian + agent second brain: vault slash commands in ~/.claude/commands/
  and ~/.cursor/commands/ (context, today, week, trace, connect, ghost, challenge, ideas,
  graduate, closeday, close-day, drift, emerge, schedule, contradict, compound, bloom,
  stranger, map, learned, weekly-learnings, backlinks, xdaily, plus wrap-up into the vault).
  Primary vault YOUR_OBSIDIAN_VAULT. Honor vault `CLAUDE.md` (raw/ immutable;
  wiki writes + index + log). Prefer Obsidian CLI when obsidian help works; else rg/Read on disk.
---

# Obsidian second brain (Vin codebook)

## When to use

User says second brain, vault thinking, Internet Vin codebook, or any slash command in the map below.

## Constant

- **Vault:** `YOUR_OBSIDIAN_VAULT`
- **Routing:** Read **`CLAUDE.md`** at the vault root before creating or moving wiki pages.

## Tooling

1. **Obsidian CLI** (`obsidian`): only if Obsidian is running and `obsidian help` works. See skill `obsidian-cli` for syntax. Use `vault=` if multiple vaults.
2. **Fallback:** `rg`, `find`, `Read` on the vault path. Never edit `raw/` except by explicit user override.

## Slash map (Claude: `~/.claude/commands/` — Cursor: `~/.cursor/commands/`)

| Slash | File | Purpose |
|------|------|---------|
| `/context` | context.md | Life, work, state; context files, dailies, backlinks |
| `/today` | today.md | Morning review; calendar/tasks/pastes + week of dailies → plan |
| `/week` | week.md | Two-week calendar, tasks, 7–14d dailies, log |
| `/schedule` | schedule.md | Context-aware scheduling vs priorities and constraints |
| `/closeday` | closeday.md | End-of-day capture |
| `/close-day` | close-day.md | Same as `/closeday` (alias) |
| `/ghost` | ghost.md | Answer in your voice; fidelity check |
| `/challenge` | challenge.md | Pressure-test beliefs with vault history |
| `/emerge` | emerge.md | Implicit ideas, unnamed patterns |
| `/contradict` | contradict.md | Incompatible beliefs held at once |
| `/drift` | drift.md | Intentions vs behavior 30–60d; avoidance |
| `/ideas` | ideas.md | ~30d scan, cross-domain patterns → idea buckets |
| `/graduate` | graduate.md | Promote dailies into wiki pages |
| `/trace` | trace.md | Idea evolution + synonym pass |
| `/connect` | connect.md | Bridge two domains on the link graph |
| `/compound` | compound.md | Same question × three vault eras |
| `/bloom` | bloom.md | Ranked notes + Mermaid around a question |
| `/stranger` | stranger.md | Cold outside read of the vault |
| `/map` | map.md | Topology: clusters, orphans, dead ends |
| `/learned` | learned.md | Three drafts: short / personal / universal |
| `/weekly-learnings` | weekly-learnings.md | Team-email prep from the week |
| `/backlinks` | backlinks.md | Graph hygiene: missing links |
| `/xdaily` | xdaily.md | X/Twitter signal vs daily notes |

Session capture that may write **wiki** or **log**: **`wrap-up`** (`wrap-up.md`). Other commands (`recall`, `subtitle`, `audit-commit`, `debrief`, `strategy`) live in the same folder but are not vault-only.

## Install (share / clone)

GitHub: **`claude-skills`** repo **`claude-commands/`** uses **`YOUR_OBSIDIAN_VAULT`**. Copy each `*.md` into **`~/.claude/commands/`** and **`~/.cursor/commands/`**, then replace the placeholder with your vault path.

Daily value: richer notes plus these commands so the agent traces **your** graph instead of generic advice.
