# /schedule — Context-aware scheduling

**Vault:** `YOUR_OBSIDIAN_VAULT` — **`CLAUDE.md`**.

Evaluates a **scheduling request** against **calendar** (vault, paste, or ICS), **priorities** in **`wiki/`**, and **hard constraints** the user states before suggesting concrete blocks.

**Request** = text after **`/schedule`**. If empty, ask what to schedule.

## Do this

1. Extract **hard constraints** (fixed meetings, deadlines, “never mornings”, travel) from user message + any calendar artifacts in vault.
2. Read **`wiki/career/`** strategy pages, **`wiki/log.md`**, recent dailies for **priorities** vs time already spoken for.
3. Output **concrete options** (day + time block + what to defer) ranked best-first; flag **conflicts** explicitly.
4. External calendar rarely in vault unless pasted; say so honestly. No calendar API fiction.
5. Optional **`obsidian daily:append`** skeleton only if user asks and CLI works.
