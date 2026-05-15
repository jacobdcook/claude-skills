# /backlinks — Audit and strengthen the link graph

**Vault:** `YOUR_OBSIDIAN_VAULT` — **`CLAUDE.md`**.

Audits the vault’s **link graph** for **missing connections** and proposes concrete **`[[wikilinks]]`** or section stubs to add so navigation matches how you actually think.

Optional **`[cluster]`** after **`/backlinks`** limits work to one subtree (e.g. `wiki/career`).

## Do this

1. If cluster given: restrict to that path; else work across **`wiki/`** (skip `raw/` writes).
2. Find **orphans**, **near-duplicates** with different titles, and **obvious missing edges** (A mentions concept B by name but no link).
3. Output a **numbered patch list**: for each item, `file`, `suggested link target`, **one sentence** why.
4. Apply edits in small batches only after user approves each batch.
