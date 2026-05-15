# /graduate — Promote half-formed ideas into wiki pages

**Vault:** `YOUR_OBSIDIAN_VAULT` — **`CLAUDE.md`** is mandatory before writes.

Scan window default **past 14 days** of daily notes unless user picks another span.

## Do this

1. Locate daily notes (CLI **`obsidian daily:read`** + history only if feasible; else discover `Daily/**/*.md` or similar under vault via glob).
2. Extract **standalone-worthy** ideas (clear claim, recurrence, tags). Skip pure logistics unless user cares.
3. For each keeper: propose **`wiki/<domain>/kebab-title.md`** with YAML frontmatter per **`CLAUDE.md`**, links to **`[[existing]]`** notes.
4. **After user confirms which to create:** write files, **`wiki/index.md`** entries, **`wiki/log.md`** line each. Never edit **`raw/`** as “mutable”; if source was daily only, excerpt is OK in new wiki note.
