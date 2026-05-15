# /map — Full vault topology scan

**Vault:** `YOUR_OBSIDIAN_VAULT` — **`CLAUDE.md`**.

**Full vault topology**: clusters, **orphans**, **dead ends**, link density, and the **shape** of the intellectual landscape (at least across **`wiki/`**; `raw/` summarized only at folder level unless user expands).

## Do this

1. Inventory **`wiki/`** folders: file counts, **orphans** (no incoming `[[wikilinks]]` from other wiki pages), **hubs** (top out-degree via `rg` link patterns).
2. Flag **dead ends** (pages that never link outward) and **dense clusters** (tightly interlinked sets); name each cluster in plain language.
3. Optional **Mermaid** overview of major clusters (keep small enough to read).
4. Give **three hygiene actions** (merge, stub, or cross-link) prioritized by impact; apply only after user picks.
