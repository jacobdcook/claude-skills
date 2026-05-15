# LinkedIn Targeted — monthly bank, weekly plan, daily send

Targeted outreach pipeline with a mandatory human gate at every step. Scrape once a month, plan once a week, send 20/day. Claude decides who to contact so Jacob never picks from a blind 5000-person pool.

## When to use

Invoke on: `/linkedin-targeted`, **targeted plan**, **plan my week**, **how many people in the bank**, **targeted send**, **send day N**, **run the scrape**, **fill the bank**, **targeted outreach**, **bank status**

Three contexts — skill auto-detects which one applies:
- No bank yet, or bank low → offer Phase 1 (scrape)
- Bank exists, no current week plan → offer Phase 2 (plan)
- Plan exists with pending days → offer Phase 4 (send today)

## Auto-execution behavior

On skill invocation, Claude **automatically**:
1. Reads `TARGETED_BANK.xlsx` status (or reports "no bank yet")
2. Reads `TARGETED_WEEKLY_PLAN.json` status (or reports "no plan yet")
3. Presents a status block and asks Jacob what to do

Claude **never auto-runs** Phase 1 (scrape), Phase 2 (plan), or Phase 4 (send) without explicit approval. All three open a browser or call Claude API and cost real time/money.

## Paths

```text
DIR=/home/z1337/Desktop/PROJECTS/LinkedIn Automator
PY=$DIR/.venv/bin/python3
BANK=$DIR/TARGETED_BANK.xlsx
PLAN=$DIR/TARGETED_WEEKLY_PLAN.json
```

## Approval keywords

| Jacob says | Agent may run |
|------------|---------------|
| `scrape` / `fill the bank` / `run scrape` | Phase 1 — monthly scrape |
| `plan this week` / `generate plan` / `plan` | Phase 2 — weekly plan generation |
| `plan looks good` / `go` / `lock it in` | Phase 3 — lock plan as-is, proceed to Phase 4 |
| `fix [name]` / `move [name] to day N` / `skip [name]` | Phase 3 — adjust plan before locking |
| `send` / `send today` / `run day N` | Phase 4 — send today's 20 (or explicit day N) |
| `abort` | Stop everything |

---

## Phase 0 — Status check (auto-run on invocation)

Run both status commands immediately when the skill is invoked:

```bash
cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 run_targeted_search.py --status
```
```bash
cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 run_plan_day.py --status
```

Present a clean block:

```
TARGETED BANK
  Total people:          712
  Unscored:              412
  Scored (Strong/Good):  187 available
  Weeks of outreach:     ~1.8 weeks remaining

THIS WEEK'S PLAN  (Week 2026-W21)
  Day 1: 20/20 sent
  Day 2:  0/20 pending  <-- next
  Day 3:  0/20 pending
  Day 4:  0/20 pending
  Day 5:  0/20 pending
```

Then ask exactly one of these depending on state:

- **No bank:** "No bank yet. Say `scrape` to run the first targeted scrape (~30 min, opens browser)."
- **Bank low (< 2 weeks remaining):** "Bank running low (~X weeks left). Say `scrape` to top it up, or `plan` to use what's there, or `send` to run today's day."
- **Bank OK, no plan:** "Bank looks healthy. No plan for this week yet. Say `plan` to generate this week's 100 targets, or `send` to pull from last week's plan if one exists."
- **Bank OK, plan active:** "Day N is ready with X contacts (Y Strong + Z Good). Say `send` to run today, or `plan` to regenerate this week's plan."

**STOP.** Wait for Jacob's reply.

---

## Phase 1 — Monthly scrape (only when Jacob says `scrape`)

Takes ~30-40 min. Adds ~700 people across 7 queries × 10 pages. Dedupes — never adds someone already in the bank.

Open a terminal:

```bash
gnome-terminal -- bash -lc 'export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"; cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 run_targeted_search.py; exec bash'
```

Tell Jacob: "Scrape running. Watch the terminal — it'll show pages scraped and new contacts found per query. I'll wait."

**STOP.** Poll for the bank file's modification time, or wait for Jacob to say "done" / "it finished".

After completion, re-read status and report:
```
Scrape complete.
  Added: 687 new people to TARGETED_BANK.xlsx
  Bank total: 1,399 people
  Weeks of outreach available: ~13 weeks

Say `plan` to generate this week's targets, or `send` if you want to use last week's plan.
```

---

## Phase 2 — Weekly plan (only when Jacob says `plan` or `plan this week`)

No browser needed. Calls Claude haiku once per unscored person. Takes ~5-10 min depending on bank size.

Run inline (not in gnome-terminal — output is readable without browser):

```bash
cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 generate_weekly_plan.py
```

While it runs, show: "Scoring unscored people and building the week's plan. This may take a few minutes — no browser needed."

After completion, read `TARGETED_WEEKLY_PLAN.json` and present the full deck:

```
WEEK 2026-W21  (100 total — 25 Strong + 75 Good)

Day 1  (8 Strong + 12 Good):
  [S] Jane Smith — Detection Engineer @ Sutter Health, Sacramento
       2nd degree | Applied company match
       https://linkedin.com/in/janesmith
  [S] Marcus Lee — SOC Analyst II @ UC Davis Health, Sacramento
       2nd degree | Exact role + location match
       https://linkedin.com/in/marcuslee
  [G] Priya R — Security Analyst @ Intel, Santa Clara
       3rd degree | CySA+ cert overlap
  ...

Day 2  (5 Strong + 15 Good):
  ...

Day 3-5: (summary only unless Jacob asks for full list)
```

Then ask:
```
Plan ready. Options:
  `go` — lock it in and send Day 1 now
  `plan looks good` — same as go
  `send day 2` — skip to a specific day
  `move [name] to day 1` — promote someone
  `skip [name]` — remove from plan
  `show day 3` — expand a day's full list
```

**STOP.** Wait for Jacob's response.

---

## Phase 3 — Adjust plan (only if Jacob requests changes)

When Jacob says `move X to day 1`, `skip Y`, `show day N`, etc.:

- Show the change: "Moving Jane Smith from Day 3 → Day 1. Dropping Marcus Lee."
- Apply it by editing `TARGETED_WEEKLY_PLAN.json` (update `day` field, or set `status: "skip"`).
- Re-show the affected days briefly.
- Ask `go` again if not already confirmed.

Do NOT re-run `generate_weekly_plan.py` unless Jacob explicitly says "regenerate" or "start over".

---

## Phase 4 — Send today's day (only when Jacob says `send` or `send day N`)

**Step 4a — Load batch:**

```bash
cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 run_plan_day.py
```

(Or `--day N` for a specific day.)

This writes today's 20 contacts to `DAILY_TARGETS_BATCH.md` and opens a gnome-terminal with `run_daily.py --draft` automatically.

Tell Jacob: "Batch loaded. Draft terminal opened — watch profiles load and notes generate. I'll wait for the drafts to complete."

**STOP.** Wait for Jacob to say "drafts done" / "done" / "ready to audit".

**Step 4b — Mandatory audit + rewrite (auto-run before showing Jacob anything):**

Read `DAILY_DRAFTS.json` AND `~/.claude/user_profile.md` (Jacob's full context). For every contact, check and fix before presenting.

**Red-flag checklist — auto-fix every flagged item:**
- Broken grammar: "noticed. Your" / "I'm. Currently" / "about. Your" — ollama tokenizer artifact, always rewrite
- Fabricated school/cert/tool overlap not confirmed in the profile snippet — remove the claim, rewrite
- Truncated company string pasted verbatim from scrape — fix
- Generic close with no specific hook — rewrite
- Off-lane contact (QA, marketing, sustainability, unrelated field) — set `status: skip`
- Note >300 chars — trim
- Em dash anywhere in note — remove

**Rewrite rules:**
- Use Jacob's REAL context: WGU MS Cyber (in progress), Sac State BS CS, Sec+/CySA+/PenTest+/SecurityX/CSAP, labs: Wazuh SIEM, Okta detection engine, Stryker Intune detection pack, Azure hardening, SOAR-lite
- Anchor on ONE specific thing from their real profile snippet (role, company, cert, tool, location)
- Only use a shared hook (school, group, tool) if VERIFIED in the profile snippet
- Target 180-270 chars. No em dashes. No generic flattery.
- Format: "Hi [First], [specific hook from their profile]. [Jacob 1-line context]. [soft CTA]."

Apply all rewrites directly to `DAILY_DRAFTS.json` before showing the audit deck.

Then output one block per contact:

```
N. {name}  [REWRITE / SKIP / OK]
   Action: what was wrong and what changed (or "clean")
   Note ({chars} chars): full note verbatim
```

Summary line: `N ready to send, N skipped (reason), N unchanged`.

Then ask exactly:

```
Audit complete. Reply with: send | fix [name] | skip [name] | abort
```

**STOP.** Never run `--send-drafts` until Jacob approves.

**Step 4c — Send (with auto-recovery fallback):**

Only after `send` or confirmed `fix and send`:

```bash
gnome-terminal -- bash -lc 'export DISPLAY=:0 XAUTHORITY="${XAUTHORITY:-$HOME/.Xauthority}"; cd "/home/z1337/Desktop/PROJECTS/LinkedIn Automator" && .venv/bin/python3 run_daily.py --send-drafts; exec bash'
```

**Auto-recovery (if a send fails):**
If the script reports a failed contact (UI not found, button unreachable, textarea missing), automatically:
1. Pull the next unplanned Strong or Good contact from `TARGETED_BANK.xlsx`
2. Generate a fresh note for them using the audit/rewrite rules from Phase 4b
3. Queue them as a replacement in the batch
4. Retry the failed slot

This matches `/linkedin-connect-gated` fallback behavior — never let a single UI glitch drop the day's count.

Watch for completion. Report: N sent, N failed, N skipped, N auto-recovered.

---

## Phase 5 — Wrap-up

After sends complete, print:

```
LinkedIn Targeted — YYYY-MM-DD  (Day N of Week W)
Sent: N/20 | Failed: N | Skipped: N
Weekly count so far: N/150
Plan remaining: Day X, Day Y, Day Z
Bank: ~N weeks of outreach left
```

Offer: "Say `engage yes` to run 25 auto-comments on the feed, or done."

---

## Failure cheatsheet

| Symptom | Action |
|---------|--------|
| Login redirect during scrape or draft | `setup_session.py jacob` (Phase 0 of /linkedin-connect-gated) |
| Bank file missing | Run Phase 1 scrape |
| No Strong/Good in bank | Bank full of Stretch/Skip — widen queries in config.json or run new scrape with different keywords |
| Plan file missing | Run Phase 2 |
| `generate_weekly_plan.py` Claude errors | Check `claude` CLI is in PATH; fallback: run with `--plan-only` to skip scoring |
| Draft terminal exits early | Profile scrape issue — rerun `run_daily.py --draft` with the same batch |
| Weekly limit hit during send | Stop; report remaining from script output |

---

## Tuning queries (no code needed)

Edit `config.json` → `targeted_search.queries` to add/remove search terms.
Edit `pages_per_query` to control scrape depth (10 = ~100 results/query, takes longer; 5 = faster run).

```json
"targeted_search": {
  "pages_per_query": 10,
  "degree": "2,3",
  "geo": "sac",
  "queries": [
    "Detection Engineer Sacramento",
    "SOC Analyst Sacramento",
    "SIEM analyst California",
    "threat hunter California",
    "information security analyst Sacramento",
    "incident response analyst California",
    "security operations engineer Sacramento"
  ]
}
```
