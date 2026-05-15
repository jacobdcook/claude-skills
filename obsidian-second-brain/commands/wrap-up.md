# Wrap Up Session

End-of-session capture for **Claude Code**, **Cursor**, and **Antigravity**. Same behavior on every host: always appends `~/.claude/sessions/YYYY-MM-DD.md`, then **auto-routes** the rest: **Pinecone** (vector prose), **Mempalace** (diary + KG facts), **Obsidian LIFE_WIKI** (wiki pages, `wiki/log.md`) depending on fit. **Obsidian is not Cursor-only** — Claude Code and Antigravity runs must update `LIFE_WIKI` when Step 3 criteria match. Jacob is running Mempalace and Obsidian in parallel long-term to compare memory systems; use **both** when named facts belong in KG *and* in wiki entities.

Optional context from the user: `$ARGUMENTS`

## Steps

1. Generate a structured summary under 600 words. Sections: Topic, What we worked on, Decisions made, Key insights, Open items / Next steps, Tags. Be specific — name actual people, files, tools, and dates. No generalities.

2. Save to `~/.claude/sessions/YYYY-MM-DD.md` (append if file exists for today). **Always.**

3. **Route** (pick any that apply; do not blindly duplicate everything everywhere):
   - **Pinecone** — narrative / fuzzy-recall value. Upsert one blob; deterministic id `session-YYYY-MM-DD-HHMM` (use `upsert_memory(..., metadata={"id": ...})` from Python if the CLI cannot set id). Skip if no recall value.
   - **Mempalace** — `mempalace_diary_write` with `agent_name` = `claude-code` | `cursor` | `antigravity` (match host); AAAK entry. `mempalace_kg_add` only for specific named, dated facts (contacts, deadlines, applications, decisions). Skip triples for vague notes.
   - **Obsidian** — Primary vault: `YOUR_OBSIDIAN_VAULT`. Follow `LIFE_WIKI/CLAUDE.md`: append `wiki/log.md`, create/update `wiki/entities/` or `wiki/career/` when people, companies, or career facts change; update `wiki/index.md` for new pages. Skip wiki entity work if the session was purely mechanical with no durable wiki content (optional one-line log entry still OK).

4. Confirm in one line what was written (sessions + which backends). If Pinecone ran, include *Search via `/recall`* and the session id.

Do NOT fire if this session had no real substance.
