# /acceptlinkedin — Batch accept pending invitations + send follow-up messages

Scrapes all pending invitation profile URLs, accepts them all, drafts personalized follow-up messages via Qwen, presents a Claude audit gate, then sends on approval.

## When to use
Trigger: `/acceptlinkedin`, "accept all invitations", "accept pending requests", "process pending invites"

## Flow overview
Phase 1 → scrape URLs + accept all + draft messages → `ACCEPT_DRAFTS.json`  
Phase 2 → Claude reads JSON and presents audit deck  
Phase 3 → Jacob approves with `send` | `fix and send` | `abort`  
Phase 4 → Send messages from `ACCEPT_DRAFTS.json`

---

## Paths

```text
AUTOMATOR=/home/z1337/Desktop/PROJECTS/LinkedIn Automator
PY=$AUTOMATOR/.venv/bin/python3
ACCEPT_DRAFTS=$AUTOMATOR/ACCEPT_DRAFTS.json
```

---

## Phase 1 — Accept + Draft

Spawn gnome-terminal so the browser and logs are visible:

```bash
gnome-terminal -- bash -lc 'export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"; cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 accept_and_draft.py --mode draft; exec bash'
```

Script does:
1. Goes to invitation-manager/received/
2. Scrolls and scrapes all profile URLs (before touching any Accept button)
3. Accepts all pending invitations
4. Visits each profile, scrapes text, drafts a personalized greeting via Qwen
5. Saves all drafts to `ACCEPT_DRAFTS.json` with `status: "ready"`
6. Exits

Wait for terminal to print `Done. Now tell Claude to audit ACCEPT_DRAFTS.json.` before proceeding.

---

## Phase 2 — Audit deck (Claude reads the JSON)

Read `ACCEPT_DRAFTS.json`. For every entry with `status: "ready"`, output one block:

```markdown
### N. {name}
- **URL:** {profile_url}
- **Headline:** {headline}
- **Message ({message_chars} chars):**
  > {message verbatim}
- **Red flags:** (blank if clean; otherwise list: em dashes, AI fragments like "I noticed. Your", generic flattery without a specific anchor, missing first name, over 200 chars)
```

Then summary line: `Ready: N | Skipped/failed: N`

**Stop.** Ask exactly:

```
Audit complete. Reply: send | fix and send | abort
```

---

## Approval keywords

| Jacob says | Action |
|---|---|
| `send` | Run Phase 4 |
| `fix and send` | Propose JSON edits → Jacob confirms → Phase 4 |
| `abort` | Stop. No messages sent. |

---

## Phase 3 — Fix path (only for `fix and send`)

1. List each change: name, field (message/status), old → new.
2. Apply edits to `ACCEPT_DRAFTS.json`.
3. Re-show edited blocks. Confirm `send` before Phase 4.

---

## Phase 4 — Send

```bash
gnome-terminal -- bash -lc 'export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"; cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 accept_and_draft.py --mode send; exec bash'
```

Script visits each `ready` profile, clicks Message, types the message, clicks Send, closes the window, marks `status: "sent"`.

---

## Message tone rules (for fix path and audit red flags)

Per the note-writing philosophy in `linkedin-connect-gated`:
- Start with `Hey {first_name},`
- Reference ONE specific thing from their profile (role, company, cert, tool)
- No em dashes
- No `I noticed.` or `Your [thing]` fragments
- No ungrounded flattery ("impressive experience")
- If recruiter: mention targeting detection engineer / SOC analyst roles
- If peer: acknowledge shared lane (certs, school, job hunting)
- If senior practitioner: genuine curiosity about their day-to-day
- Under 200 chars ideally

---

## Failure reference

| Symptom | Fix |
|---|---|
| Login redirect | Run `setup_session.py jacob` (Phase 0 from linkedin-connect-gated) |
| `failed_no_url` | Profile URL wasn't extractable from invite card; skip is correct |
| `failed_no_msgbtn` | Message button not found on profile; may need to retry manually |
| `failed_send` | Message typed but send failed; rerun with `--mode send --retry-failed` — no JSON editing needed |
| Empty `ACCEPT_DRAFTS.json` | No pending invitations, or all were non-person invites (pages/newsletters) |
