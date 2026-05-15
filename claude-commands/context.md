# /context — Load full context about life, work, and current state

**Vault:** `YOUR_OBSIDIAN_VAULT` — follow **`CLAUDE.md`** (`raw/` read-only for agents; **`wiki/`** for curated edits + **`wiki/index.md`** + **`wiki/log.md`** when creating pages).

Reads **context files**, **daily notes**, and follows **backlinks** to build a **complete picture**. User may add focus after **`/context`**. Use chat context if absent.

## Do this

1. Read **`wiki/index.md`**, **`wiki/overview.md`** (if present), **`wiki/log.md`** tail (latest changes).
2. Scan **last ~7 days** of edits: prioritize `wiki/career/`, `wiki/concepts/`, `wiki/technical/`, **`raw/notes/`** (read-only) via `mtime` discovery or `rg` on dates in YAML if present.
3. For the user’s focus (or default “whole life”): **`rg`** hub pages and run **backlink tracing** from 2–4 anchor notes (follow `[[wikilinks]]` one hop out, cite paths).
4. If **`obsidian help`** works: `obsidian search` for recent-ish keywords (priorities, project names) with a sensible limit.
5. Produce one **compact brief**: active projects, stated priorities, open loops, recurring themes **with file path citations**.
6. If user wants persistence, offer a short stub under **`wiki/sources/`** plus index/log updates (do not write without confirmation).
