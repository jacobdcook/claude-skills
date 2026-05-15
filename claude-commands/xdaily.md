# /xdaily — Surface X (Twitter) ideas not yet in daily notes

**Vault:** `YOUR_OBSIDIAN_VAULT` — **`CLAUDE.md`**.

Pulls **recent tweets** (or bookmarks/exports the user keeps in vault) and compares against **recent daily notes** so observations land in the graph instead of staying only on X.

## Do this

1. Look under vault for **`X/`**, **`twitter/`**, **`social/`**, or similar folders, or files mentioning `twitter.com` / `x.com`. If nothing exists, ask the user for a **paste, export path, or date range**; do not pretend you have API access you do not have.
2. Summarize **5–15 tweet-sized ideas** with dates/handles if known.
3. **`rg`** the last **7–14 days** of daily notes for overlap; flag ideas **not** reflected in dailies.
4. Output **candidate bullets** to append to today’s daily (wording ready to paste) plus optional **`wiki/`** stub paths if something is thesis-level.
5. Append or create files only if the user confirms.
