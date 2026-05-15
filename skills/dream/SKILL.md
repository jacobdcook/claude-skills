---
name: dream
description: Memory consolidation pass over auto-memory files. Reconciles contradictions, prunes stale entries, merges near-duplicates, and keeps the MEMORY.md index tight. Run weekly or when the index gets bloated. Adapted from Claude Code's autoDream system.
---

# Dream: Memory Consolidation

You are performing a dream — a reflective pass over your memory files. Synthesize what you've learned recently into durable, well-organized memories so that future sessions can orient quickly.

Memory directory: `/home/z1337/.claude/projects/-home-z1337-Desktop-PROJECTS-JOB-TRACKING/memory/`
Global memory: `/home/z1337/.claude/user_profile.md` and `/home/z1337/.claude/CLAUDE.md`

---

## Phase 1 — Orient

- `ls` the memory directory to see what already exists
- Read `MEMORY.md` to understand the current index
- Skim existing topic files so you improve them rather than creating duplicates
- Note which entries point to files that no longer exist (broken pointers)

## Phase 2 — Gather recent signal

Look for new information worth persisting. Sources in rough priority order:

1. **Recent conversation context** — what did the user reveal in the last week that isn't in memory yet?
2. **Existing memories that drifted** — facts that contradict reality now (e.g., a contact's role changed, an interview happened)
3. **Auto-saved memories from the AUTO-PERSIST CONTEXT rules** — did Claude Code save anything new since the last dream?

Don't exhaustively read transcripts. Look only for things you already suspect matter.

## Phase 3 — Consolidate

For each thing worth remembering, write or update a memory file. Use the four-type taxonomy from CLAUDE.md (user / feedback / project / reference).

Focus on:
- **Merging** new signal into existing topic files rather than creating near-duplicates
- **Converting relative dates** ("yesterday", "last week") to absolute dates so they remain interpretable after time passes
- **Deleting contradicted facts** — if today's investigation disproves an old memory, fix it at the source
- **Combining** memories that are about the same person/project but split across files

## Phase 4 — Prune and index

Update `MEMORY.md` so it stays under 200 lines AND under ~25KB. It's an **index**, not a dump — each entry should be one line under ~150 characters: `- [Title](file.md) — one-line hook`. Never write memory content directly into it.

- Remove pointers to memories that are now stale, wrong, or superseded
- Demote verbose entries: if an index line is over ~200 chars, it's carrying content that belongs in the topic file — shorten the line, move the detail
- Add pointers to newly important memories
- Resolve contradictions — if two files disagree, fix the wrong one
- Delete broken pointers (files that no longer exist)

---

## Output

Return a brief summary in this format:

```
## Dream Summary — YYYY-MM-DD

**Consolidated:** N entries merged or rewritten
**Pruned:** N stale/duplicate entries removed
**New:** N new memories added from recent signal
**Resolved:** N contradictions fixed

Key changes:
- ...
- ...
```

If nothing changed (memories are already tight), say "Memory is consolidated, no changes needed."
