# /trace — Idea evolution across the vault

**Vault:** `YOUR_OBSIDIAN_VAULT` — **`CLAUDE.md`**; **`raw/`** read-only.

**Topic:** take from text after **`/trace`**. Ask once if empty.

## Do this

1. **`rg -n -i`** the topic across `wiki/` and `raw/` (include `[[wikilinks]]` variants).
2. If CLI works: **`obsidian search query="TOPIC"`** + **`obsidian backlinks`** for the strongest anchor note if identifiable.
3. Build **timeline**: first clear mention (path + inferred date from YAML/filename/context), trajectory of how wording or conclusions shift, current linked notes (**`[[...]]`**).
4. Respond in markdown **with citations**. Offer optional **`wiki/sources/trace-YYYY-MM-DD-topic-slug.md`** + index/log (confirm first).
