# /bloom — Notes that “light up” around a question

**Vault:** `YOUR_OBSIDIAN_VAULT` — **`CLAUDE.md`**.

Surfaces **every vault note that activates** around a question: relevance ordering, **link paths**, and a compact **graph view** (Markdown + optional **Mermaid**). Not a live Obsidian canvas unless the user runs something locally; this is the **agent-rendered** equivalent.

User puts the **question** after **`/bloom`**.

## Do this

1. Tokenize the question; **`rg`** **`wiki/`** (and agreed `raw/` reads) for hits; score files by hit count + recency + hub status (links from **`wiki/index.md`**).
2. Build a **ranked table**: path, one-line why it matters, rough relevance **high/med/low**.
3. Add **Mermaid `graph LR`** (or `flowchart`) linking the **top 8–15** notes via actual `[[wikilinks]]` found in bodies (no invented edges).
4. If the graph is sparse, say so and name **2–4** missing note types that would help next time.
