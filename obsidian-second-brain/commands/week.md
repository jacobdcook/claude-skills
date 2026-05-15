# /week — Weekly review from the vault

**Vault:** `YOUR_OBSIDIAN_VAULT` — **`CLAUDE.md`**.

Pulls a wider window than **`/today`**: roughly **two weeks** of calendar context (if present in vault or user pastes), **open tasks** across daily notes and **`wiki/`**, **7–14 days** of daily notes, **`wiki/index.md`** / **`wiki/log.md`**, and **recent messages** only if the user pasted them or they live in vault exports.

## Do this

1. Discover daily notes for the last **14 days** (same rules as **`/today`**: Obsidian CLI `obsidian daily:read` where possible, else glob `Daily/`, `daily/`, etc.).
2. **`rg`** for `- [ ]` in those dailies plus high-signal **`wiki/`** pages; read **`wiki/log.md`** for what moved this week.
3. Calendar: use ICS links or embedded schedules in vault if any; otherwise ask once for a paste of the next two weeks or skip.
4. Output **one structured weekly brief**: wins, misses, themes, **top 5 carry-overs** into next week, each bullet **cited** to a path.
5. Writes to vault only after explicit user confirmation.
