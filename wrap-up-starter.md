---
name: wrap-up
description: End-of-session capture. Saves a structured summary to a dated sessions log and writes relevant notes to your Obsidian vault.
---

# Usage
/wrap-up [optional context]

# Prerequisites
- Obsidian installed with a vault path you know (e.g. `~/Documents/ObsidianVault/`)
- `~/.claude/sessions/` directory exists (Claude will create it if not)

# Behavior

## Step 1 — Generate structured summary (under 400 words)
- **Topic**: One-line focus of the session
- **What we worked on**: Specific tasks completed
- **Decisions made**: Choices made this session worth remembering
- **Key insights**: Lessons learned, things that surprised you
- **Open items**: Unfinished work or follow-ups (use absolute dates, not "tomorrow")
- **Tags**: 5–10 lowercase hyphenated keywords

Be specific. Name actual people, tools, and files. Do not generalize.

## Step 2 — Save to sessions log
Append to `~/.claude/sessions/YYYY-MM-DD.md` (create if new, append if file already exists for today).

## Step 3 — Write to Obsidian
Save a note to your Obsidian vault at `[YOUR_VAULT_PATH]/sessions/YYYY-MM-DD.md`.
Include only what is worth finding later — contacts met, decisions made, things to follow up on.
Skip routine task completions that have no future value.

## Step 4 — Confirm
One line: `Saved to sessions log and Obsidian — YYYY-MM-DD.`

---

## Upgrading later
Once you have Pinecone and Mempalace set up, swap this file for the full version at:
https://github.com/jacobdcook/claude-skills/blob/main/wrap-up.md

That version adds semantic search recall and a persistent knowledge graph on top of what this one does.

Do NOT fire if this session had no real substance.
