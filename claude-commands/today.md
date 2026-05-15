# /today — Morning review and prioritized day plan

**Vault:** `YOUR_OBSIDIAN_VAULT` — **`CLAUDE.md`** rules apply.

Pulls **calendar** (if present in vault or user pastes), **tasks**, **iMessages** or other chats **only if the user supplies exports or pasted snippets**, and roughly the **past week** of **daily notes** into **one prioritized plan** for the day.

## Do this

1. If CLI works and daily notes plugin is active: **`obsidian daily:read`** (Obsidian focused on this vault). Else **`rg`**/`Read` Obsidian daily folder (often `Daily/` or `daily/` — discover under vault root).
2. Pull **last ~7 days** of daily notes for open threads; merge with **today’s** `- [ ]` tasks and recent **`wiki/`** priorities.
3. **Calendar:** use ICS/links in vault or user paste; otherwise say “paste today’s obligations or skip.” **Messages:** only if user pasted or files exist under an agreed export path; never invent iMessage content.
4. Output **one prioritized list**: must-do / should-do / optional, grounded in **what they already marked important this week** in notes.
5. Optional append: **`obsidian daily:append`** with bullets only if CLI works **and** user implied they want tasks written down.
