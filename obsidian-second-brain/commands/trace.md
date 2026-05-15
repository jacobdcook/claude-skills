# /trace — How an idea evolved across the vault

**Vault:** `YOUR_OBSIDIAN_VAULT` — **`CLAUDE.md`**; **`raw/`** read-only.

Tracks how an **idea evolved over time** across the vault. Builds a **small synonym / phrase vocabulary** for the topic, then traces the **full arc** (first mention → shifts → today).

**Topic:** text after **`/trace`**. Ask once if empty.

## Do this

1. Collect **surface forms**: literal string, synonyms, abbreviations, related entity names (**`rg -i`**).
2. **`rg -n`** across `wiki/` and `raw/` (include `[[wikilinks]]` variants). If CLI works: **`obsidian search`** + **`obsidian backlinks`** on the strongest hub note.
3. Build **timeline**: first clear mention (path + inferred date), inflection points, current stance and **`[[links]]`** neighborhood.
4. Respond in markdown **with citations**. Optional **`wiki/sources/trace-...md`** + index/log only after user confirms.
