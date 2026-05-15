# /context — Load full vault life/work state

**Vault:** `YOUR_OBSIDIAN_VAULT` — follow **`CLAUDE.md`** (`raw/` read-only for agents; **`wiki/`** for curated edits + **`wiki/index.md`** + **`wiki/log.md`** when creating pages).

User may add focus after **`/context`**. Use chat context if absent.

## Do this

1. Read **`wiki/index.md`**, **`wiki/overview.md`** (if present), **`wiki/log.md`** tail (latest changes).
2. Scan **last ~7 days** of edits: prioritize `wiki/career/`, `wiki/concepts/`, `wiki/technical/`, **`raw/notes/`** (read-only) via `mtime` discovery or `rg` on dates in YAML if present.
3. If **`obsidian help`** works: `obsidian search` for recent-ish keywords (priorities, project names) with a sensible limit.
4. Produce one **compact brief**: active projects, stated priorities, open loops, recurring themes **with file path citations**.
5. If user wants persistence, offer a short stub under **`wiki/sources/`** plus index/log updates (do not write without confirmation).
