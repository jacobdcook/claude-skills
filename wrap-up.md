---
name: wrap-up
description: End-of-session capture. Saves structured summary to sessions log, Pinecone (semantic search), and Mempalace (knowledge graph + diary). Run at end of any session with real substance.
---

# Usage
/wrap-up [optional context]

# Prerequisites
- `~/.claude/pinecone_memory.py` with `PINECONE_API_KEY` env var set
- Mempalace MCP installed and running (`npx -y @mempalace/mcp` or equivalent)
- `~/.claude/sessions/` directory exists

# Behavior

## Step 1 — Generate structured summary (under 600 words)
- **Topic**: One-line focus of the session
- **What we worked on**: Specific tasks, files changed, tools used
- **Decisions made**: Architectural, strategic, or personal choices made this session
- **Key insights**: Lessons learned, patterns validated, surprises
- **Open items / Next steps**: Unfinished work, pending responses, follow-up dates (absolute dates only)
- **Tags**: 5–10 lowercase hyphenated keywords

Be specific. Quote actual decisions. Name actual people, files, and tools. Do NOT generalize.

## Step 2 — Save to sessions log
Append to `~/.claude/sessions/YYYY-MM-DD.md` (create if new, append if exists).

## Step 3 — Upsert to Pinecone
Run: `python3 ~/.claude/pinecone_memory.py upsert "..."` with the full summary as a single text blob.
Use deterministic ID `session-YYYY-MM-DD-HHMM` so re-runs upsert rather than duplicate.

## Step 4 — Write to Mempalace
Two calls:

**4a. Diary entry** via `mempalace_diary_write`:
- `agent_name`: name of the current AI tool writing this (claude-code, cursor, antigravity)
- `topic`: session topic tag
- `entry`: compressed AAAK format — entity codes, *emotion markers*, pipe-separated fields. Cover who was involved, what was decided, what's pending, what was surprising.

**4b. KG triples** via `mempalace_kg_add` — one call per discrete named fact:
- New contacts met: subject=`Person A`, predicate=`met`, object=`Person B`
- Pending intros: subject=`Person A`, predicate=`will_intro`, object=`Person B`
- Decisions: subject=`[YOUR_NAME]`, predicate=`decided`, object=`[decision]`
- Deadlines: subject=`[YOUR_NAME]`, predicate=`deadline`, object=`[task — YYYY-MM-DD]`
- Jobs applied: subject=`[YOUR_NAME]`, predicate=`applied_to`, object=`[role at company]`
- Jobs resolved: subject=`[YOUR_NAME]`, predicate=`outcome`, object=`[role — result]`

Only add KG triples for facts that are specific, named, and dated. Skip generic observations.

## What goes where

| System | What to store | Why |
|---|---|---|
| Sessions file | Full structured summary | Human-readable log, Obsidian-compatible |
| Pinecone | Full text blob | Semantic recall via `/recall` |
| Mempalace diary | Compressed AAAK summary | Cross-session AI memory, agent-readable |
| Mempalace KG | Named facts, contacts, deadlines | Queryable structured facts |

## Step 5 — Confirm
One line: `Saved to memory archive — ID session-YYYY-MM-DD-HHMM. Search via /recall.`

Do NOT fire if the session had no real substance.
