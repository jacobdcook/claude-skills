# LinkedIn daily — full gated flow (human sends only)

End-to-end LinkedIn connection automation **with a mandatory human audit** before any invite leaves the browser. Mirrors what unattended cron would do (generate → personalize → send → optional engage), **except send and engage never run until Jacob explicitly approves in chat.**

## When to use

Invoke on phrases like: **linkedin gated flow**, **linkedin daily with approval**, **run linkedin drafts then wait for me**, **safe linkedin batch**, **phases** (this skill uses Phase 0–6).

Default codebase: **LinkedIn Automator** (Playwright + `DAILY_DRAFTS.json`). Do not confuse with **JOB TRACKING** `run_daily.py` (different path and flags).

## Auto-execution behavior (agent runs, human approves sends)

On skill invocation, **agent automatically executes** through Phase 2:

1. **Read `DAILY_DRAFTS.json`** and count sendable rows (`ready`, `needs_review`, `failed`).
2. **If no sendable rows** (all `sent`, empty, or stale date): **auto-run Phase 1** via `gnome-terminal` (opens terminal + headed browser).
3. **Poll for completion** by watching the draft file's `generated` timestamp or waiting ~90-120 seconds, then re-read.
4. **Auto-present Phase 2 audit deck** with full note text for every sendable target.
5. **STOP and wait for human approval** (`send` | `fix and send` | `abort`) before Phase 4/5.

This means Jacob says `/linkedin-connect-gated` and watches the browser work, then reviews the audit when it appears. No copy-paste commands needed for generate/draft.

## Note-writing philosophy (mandatory for fix path and audit)

Jacob connects with people to **start conversations**, not to collect contacts. Every note should reflect one of these honest reasons:

**1. You have experience I want to understand**
Use when the person is further along in a career Jacob is targeting. Reference something specific from their profile — their company, role, a cert they earned, a tool they use — and express genuine curiosity. Never fake expertise or shared background you don't have.

> "I'm building toward [role] and curious what [specific thing from their profile] actually looks like day-to-day."

**2. I applied at your company and want an inside perspective**
Use when Jacob applied to a role there (check `score_reason` or `fallback_note` for application context). Be direct about the application; don't hide it.

> "I applied for a [role] at [company] and wanted to connect with someone who knows the company well."

**3. We're in the same lane at similar stages**
Use for peer connections — same certs, same school, both job hunting, both in Sacramento. Honest solidarity, not flattery. Reference the real overlap.

> "Security+, SOC roles, Sacramento — same spot. 9 mutuals too."

**4. You're a recruiter who works my target roles/platforms**
Use for recruiters. Be specific about role lane and platforms you're visible on (Dice, Indeed, LinkedIn). Don't generic-pitch. Name the role.

> "Targeting detection engineer roles. Active on Dice and Indeed. Worth connecting."

**Rules that override AI-generated notes:**
- Never write "I noticed. Your [thing]" — this is an AI fragment, not a sentence
- Never claim shared experience Jacob doesn't have ("we both have background in X")
- Never use generic flattery ("Impressive experience", "Your journey inspires me") without something specific underneath it
- Off-lane targets (no security overlap, no application context, no shared school/cert/location) should get a note that honestly explains a curiosity angle — or be flagged for skip
- All notes must be readable by a person who knows nothing about Jacob except what's in the note itself

## Iron rules (skill failure if violated)

1. **Never** run `run_daily.py --send-drafts` or `linkedin_engager.py` until Jacob replies with an approval keyword (below).
2. **Never** skip the audit section: every pending target gets **context + full note** (or intentional truncation called out).
3. **Never** edit `DAILY_DRAFTS.json` without showing a **clear diff/summary** of field-level changes first.
4. **Never** tell Jacob to use `daily_linkedin.sh` with interactive `read -p` when this skill is active; use explicit phases here.
5. Headed browser **and** a **real scrolling terminal**: for Phase 1 / 4 / 5 (and interactive Phase 0 session refresh), launch via **`gnome-terminal -- bash -lc '…'`** per **Visible terminal window** so **every log line** appears beside Chromium. Do not pipe stdout/stderr to `/dev/null`. If `gnome-terminal` is unavailable or Jacob opts in, use Cursor Agent terminal **and** tell him to **pin the Terminal panel** so logs stay visible while the browser runs.
6. Workspace text rules: **`daily-batch-notes.mdc`** (no `nan`/`None`/blank company junk), **`linkedin-messages.mdc`**, **`no-em-dash-and-copy-boxes.mdc`**.

## Paths

```text
AUTOMATOR=/home/z1337/Desktop/PROJECTS/LinkedIn Automator
PY=$AUTOMATOR/.venv/bin/python3
DRAFTS=$AUTOMATOR/DAILY_DRAFTS.json
```

## Visible terminal window (interactive default)

Jacob watches headed Chromium **and** needs command logs in a **dedicated terminal window**. Opening Playwright from Cursor Agent terminal alone hides scrolling logs behind the chat unless he pins the panel. **Default:** spawn **`gnome-terminal`** so stdout/stderr stream beside the browser.

**Launcher shape** (swap only `<COMMAND_HERE>`):

```bash
gnome-terminal -- bash -lc 'export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"; cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && <COMMAND_HERE>; exec bash'
```

**Phase 0** (`setup_session.py`, Jacob completes SSO in the opened browser):

```bash
gnome-terminal -- bash -lc 'export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"; cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 setup_session.py jacob; exec bash'
```

**Phase 1** (`--generate --draft`; add `--targeted` before `--generate` if Jacob asked):

```bash
gnome-terminal -- bash -lc 'export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"; cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 run_daily.py --generate --draft; exec bash'
```

**Phase 4** send:

```bash
gnome-terminal -- bash -lc 'export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"; cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 run_daily.py --send-drafts; exec bash'
```

**Phase 4** subset (replace URLs):

```bash
gnome-terminal -- bash -lc 'export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"; cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 run_daily.py --send-drafts --urls "https://..." "https://..."; exec bash'
```

**Phase 5** engage:

```bash
gnome-terminal -- bash -lc 'export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"; cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 linkedin_engager.py --auto --max 25; exec bash'
```

Adjust `DISPLAY` / `XAUTHORITY` if Jacob uses Wayland or a non-default session.

## Approval keywords (exact intent)

| Jacob says | Agent may run |
|------------|----------------|
| **`send`** | `--send-drafts` for all targets still in sendable status (after any edits he already approved). |
| **`fix and send`** | Agent proposes JSON edits → Jacob confirms → then `--send-drafts`. |
| **`abort`** | Stop. Optionally set non-sendable statuses in JSON only if Jacob asks. |
| **`send subset`** + URLs | `--send-drafts --urls URL1 URL2` only for listed URLs (still only after explicit send-class approval). |
| **`engage yes`** / **`run engage`** | After sends complete: `linkedin_engager.py --auto --max 25`. Never default-on; ask once. |

## Phase 0 — Preconditions

1. If Jacob reports login redirect or cold session: run **`setup_session.py jacob`** using the **Phase 0 `gnome-terminal` block** under **Visible terminal window** (or Jacob runs the same in a terminal he opens). Agent cannot complete SSO for him.

Bare-shell equivalent:

```bash
export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"
cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 setup_session.py jacob
```

2. Agent may run Phase 1 **only** when a GUI session exists (`DISPLAY=:0` typically).

## Phase 1 — Generate batch + drafts

**Agent:** spawn **`gnome-terminal`** with the **Phase 1** command under **Visible terminal window** (not Cursor Agent terminal only). Variants: `--targeted` before `--generate`; draft-only is `--draft` only.

Bare-shell equivalent:

```bash
export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"
cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 run_daily.py --generate --draft
```

**Pending / already-connected backfill (draft phase):** While building `DAILY_DRAFTS.json`, the script opens each profile in batch order. If there is **no Connect** and the relationship is already **Pending**, **1st** (connected), or confirmed via **More** (e.g. Remove connection, Withdraw invitation, pending text), it **marks `Message Sent? = Y`** in `LinkedIn_Contacts_Master.xlsx`, **does not** emit a draft row for that URL, and **pulls the next eligible person** from the same pool type (**Professional** vs **Recruiter**), ranked like `generate_daily_targets.py` (`build_backfill_queues`). This matches the old JOB TRACKING cron behavior so Jacob does not burn review time on people who were already pending. If the pool cannot refill, stderr will show a **SHORTFALL** line.

**VERIFY smoke-test row:** Set **`batch.backfill_verify_profile_url`** in **`config.json`** (Automator Config GUI field **Backfill verify URL**) or **`LINKEDIN_VERIFY_BACKFILL_URL`** to a full LinkedIn profile URL that already exists in **`LinkedIn_Contacts_Master.xlsx`** (someone **already invited**, **`Message Sent? = Y`**). **`generate_daily_targets.py`** prepends that profile as **line 1** of **`DAILY_TARGETS_BATCH.md`** so **`--draft`** exercises pending or connected detection and you see the pool replacement on the first slot.

**Code refs:** `LinkedIn Automator/run_daily.py` (`run_draft_generation`, `confirm_relationship_when_no_connect`), `LinkedIn Automator/generate_daily_targets.py` (`build_backfill_queues`).

Variants Jacob might ask for (same `gnome-terminal` pattern, edit the inner command):

- Targeted companies first: add `--targeted` before `--generate --draft`.
- Draft-only on existing batch (no regenerate): `--draft` only.

On non-zero exit: surface stderr tail; if login wall, Phase 0; if empty pool, suggest `linkedin_search_scraper.py --batch --geo both` from Automator docs. **Do not** loop blindly.

Wait until Phase 1 completes before Phase 2.

**Draft notes AI (`draft_ai_provider`):** Per-profile notes during `--draft` use **`draft_ai_provider`** (default **`ollama`**, local Qwen). Global **`ai_provider`** can remain **`claude`** for other scripts without spending Claude on every draft row. Config GUI: “Draft notes AI”. Override in **`config.json`** if needed.

**Mandatory Claude note rewrite (before Phase 2):** After `--draft` completes, **always** rewrite every `ready`/`needs_review`/`failed` note using the `profile_snippet` data in `DAILY_DRAFTS.json` before presenting the audit deck. Do NOT audit ollama/Qwen raw output as-is. Two paths — pick whichever is faster:

- **Inline rewrite (preferred when Claude is auditing):** Read each row's `profile_snippet` directly, write a new `note` grounded in actual profile content, update `note_chars`, write back to JSON. Do this before building the Phase 2 audit deck.
- **`--audit-drafts` CLI:** Run `python run_daily.py --audit-drafts` — sends all snippets to Claude in one batch call, rewrites notes, writes back to JSON. Use when the batch is large or Jacob prefers a terminal pass.

Either way: no note that came from ollama/Qwen should appear in the Phase 2 deck without first being rewritten using `profile_snippet` context against Jacob's background (Sac State CS, WGU MS Cybersecurity, Security+/CySA+/PenTest+/SecurityX, Wazuh SIEM lab, Okta detection engine, Azure hardening, targeting detection engineer/SOC analyst roles, Sacramento).

**Off-lane detection during rewrite:** If the `profile_snippet` shows zero overlap with cybersecurity, defense-tech, recruiter lane, or shared school/cert/location and there is no application context — set `status: skip` during the rewrite pass. Do not waste the audit deck on people Jacob has no honest reason to connect with.

**Persisted profile context:** Each draft row **`profile_snippet`** holds up to **~14k characters** (LinkedIn **`main`** sections after scroll, falling back to body text). This is the ground truth for note writing — use it.

## Phase 2 — Load drafts + mandatory audit deck

1. Read **`DAILY_DRAFTS.json`** (`targets` array).

2. **Sendable statuses** in code: `ready`, `failed`, `needs_review`. Treat **`skip`**, **`sent`**, **`email_required`** as non-send unless Jacob overrides.

3. For **each** target Jacob might still send (typically `ready`, `needs_review`, `failed`), output **one block per person** in this shape (adjust if fields missing):

```markdown
### N. {name_short}
- **URL:** …
- **Category:** …
- **Headline:** …
- **Company:** …
- **Score / reason:** … (if non-empty)
- **Status:** …
- **Connection note ({note_chars} chars):**
  > full note verbatim
- **Profile scrape sanity:** if `profile_snippet` looks like a LinkedIn login/join page (e.g. “Agree & Join”), flag **`login_wall`** and recommend retry draft row after session fix.
- **Red-flag scan:** empty line if clean; else list any of: broken AI fragment (“I noticed. Your”, “I'm. Also”, “about. Your”), false shared experience (“we both have background in X” when Jacob doesn't), generic ungrounded flattery, em-dash, placeholder text, off-lane with no curiosity hook or application context, note too long (>300 chars for LinkedIn), company field bled into note awkwardly. Also flag if the note gives no reason Jacob is connecting — it should be clear from the note alone.
```

4. Then a **summary line**: counts of `ready`, `needs_review`, `failed`, `skip`, `sent`; how many notes would fire on `--send-drafts`.

5. **STOP.** Ask exactly:

```text
Audit complete. Reply with one of: send | fix and send | abort
(Optional after send: say engage yes to run up to 25 auto-comments.)
```

**Do not** run Phase 3 until Jacob sends an approval keyword.

## Phase 3 — Fix path (only if `fix and send`)

1. List each change: row identifier (URL or name), field (`note`, `status`, etc.), old → new.
2. Apply edits to **`DAILY_DRAFTS.json`** preserving JSON structure; keep `note_chars` consistent with `len(note)` when editing `note`.
3. Re-show any edited blocks briefly. Ask **`send`** again if needed for clarity.

## Phase 4 — Send

Only after **`send`** or confirmed **`fix and send`:** use the **Phase 4 `gnome-terminal` blocks** under **Visible terminal window** (full batch or subset `--urls`).

Bare-shell equivalent:

```bash
export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"
cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 run_daily.py --send-drafts
```

Subset: same path, append `--urls 'https://…' 'https://…'`.

If browser dies mid-run: Jacob reruns **Phase 4 only** (do not regenerate drafts).

## Phase 5 — Engage (optional)

Only if Jacob explicitly asked after send: **Phase 5 `gnome-terminal` block** under **Visible terminal window**.

Bare-shell equivalent:

```bash
export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"
cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 linkedin_engager.py --auto --max 25
```

## Phase 6 — Wrap-up

Print copy-paste summary:

```text
LinkedIn gated daily — YYYY-MM-DD
Draft file: DAILY_DRAFTS.json
Sent: … / Failed: … / Skipped (pending/etc.): …
Engaged: … (or skipped)
Notes: …
```

Offer: “Say **`log it`** if you want this appended to `LIFE_WIKI/wiki/log.md` or a daily note.”

## Failure cheatsheet

| Symptom | Action |
|---------|--------|
| Feed/login redirect | `setup_session.py jacob` |
| No sendable rows | Regenerate or refill pool (`linkedin_search_scraper.py --batch --geo both`) |
| Weekly limit | Stop; report remaining from script output |
| Many “Already pending” at **send** time | Normal; script marks Y and moves on. Prefer fewer by using **draft** phase backfill above. |
| `SHORTFALL` after `--draft` | Batch had many pending/connected and pool ran out; add contacts or widen search. |

## JOB TRACKING stack (not default here)

Cron-style pipeline lives under **`/home/z1337/Desktop/PROJECTS/JOB TRACKING`** (`daily_linkedin_auto.sh`). It uses **different** `run_daily.py` flags (`--scrape-draft`, `--trust-batch-notes`). **Do not mix** with Automator paths in one run.

If Jacob wants unattended cron **without** wild sends: set **`NO_SEND=1`** so the JOB TRACKING pipeline stops **before** `--trust-batch-notes`; human runs send manually when ready.

## Workspace rule (Cursor)

**`JOB TRACKING/.cursor/rules/linkedin-gated-automator.mdc`** is a short pointer to Automator paths and draft backfill when that repo is open. **`~/.claude/skills/linkedin-connect-gated/SKILL.md`** and **`~/.cursor/skills/linkedin-connect-gated/SKILL.md`** are the same Phase 0–6 checklist (keep them identical).
