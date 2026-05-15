---
name: apply-this-week
description: Triage Jacob's scraped LinkedIn job results and produce this week's curated apply list. Reads JOBS_SCORED.xlsx, WEEKLY_JOB_RANKINGS.md, and APPLY_THESE.md from the JOB TRACKING repo, filters hard blockers (TS/SCI, military-only), prioritizes Detection/Automation/SOC II lane matches, and offers to invoke the existing tailor pipeline (vet_jobs.py + tailor_with_claude.sh) for selected jobs. Use when asked to "apply this week", "what should I apply to", "weekly job triage", "after a scrape what's next", or "find me apply targets".
license: MIT
---

# apply-this-week

Wraps Jacob's existing job pipeline. **Do not write new ranking, vetting, or tailoring code.** All heavy lifting is delegated to scripts already in `tools/`. This skill is orchestration + presentation only.

## Inputs (always check these in this order)

1. `/home/z1337/Desktop/PROJECTS/JOB TRACKING/WEEKLY_JOB_RANKINGS.md` — auto-generated ranked list from `twice_weekly_rank_apply.py`
2. `/home/z1337/Desktop/PROJECTS/JOB TRACKING/APPLY_THESE.md` — source of truth for status (✅ APPLIED, 🟢 READY TO APPLY, ⏳ TODO, ❌ CLOSED, ⚠️ DUPLICATE)
3. `/home/z1337/Desktop/PROJECTS/JOB TRACKING/all_scraped_jobs.xlsx` — applied/in-progress tracker (88 rows, has tailored_html + pdf paths)
4. `/home/z1337/Desktop/PROJECTS/JOB TRACKING/JOBS_SCORED.xlsx` — full scored corpus (~2000 rows). Only consult if rankings file is stale.
5. `/home/z1337/Desktop/PROJECTS/JOB TRACKING/job_bodies/<jobid>.txt` — scraped JD context (already on disk after a scrape run)

## Workflow

### Step 1 — Check freshness

```bash
ls -la "/home/z1337/Desktop/PROJECTS/JOB TRACKING/WEEKLY_JOB_RANKINGS.md"
```

If older than 4 days, tell Jacob and ask if he wants to regenerate with:

```bash
cd "/home/z1337/Desktop/PROJECTS/JOB TRACKING" && python3 tools/twice_weekly_rank_apply.py
```

If newer, proceed.

### Step 2 — Read state

Read `WEEKLY_JOB_RANKINGS.md` and `APPLY_THESE.md`. Build an in-memory set of already-applied/closed/duplicate job IDs from `APPLY_THESE.md` and exclude them from the rankings.

### Step 3 — Apply hard-blocker filter

Drop any job whose JD or notes contain:
- TS/SCI clearance, Top Secret, Secret clearance required
- DOD SkillBridge (military-only)
- Active duty / military separation eligible only
- US citizen + clearance combo
- Onsite-only in cities Jacob can't relocate to (default: anywhere outside CA unless explicitly remote)

Reference: `feedback_jd_eligibility_screen.md` in memory.

### Step 4 — Lane priority boost

Boost (move to top of list, mark as **PRIORITY**) any job matching:
- Title contains: "Automation Engineer", "Automations", "Detection Engineer", "Detection & Response", "SOAR", "Security Automation"
- Title contains: "SOC Analyst II", "SOC L2", "Tier 2 Analyst", "Cyber Incident Responder"
- Company on Jacob's warm-path list: CrowdStrike (Pedro), Huntress (James intro), PG&E (Cara referral), SanDisk (Damien), U.S. Silica (priority-1 from memory)

Demote (mark as **STRETCH**) any job that scored APPLY but has:
- "10+ years" or "Senior" or "Principal" in title
- Stack mismatch (Splunk-heavy when Jacob is Wazuh, Snowflake/Tableau heavy, etc.)

### Step 5 — Present curated weekly list

Output format (replace example values with real ones):

```
# This Week's Apply List — <YYYY-MM-DD>

Source: WEEKLY_JOB_RANKINGS.md (generated <date>) cross-referenced with APPLY_THESE.md.
Already applied: <N> rows excluded. Hard-blocked: <N> rows excluded.

## PRIORITY (3 to 5 — apply first, this week)

1. **<Company>** — <Title>
   - Score: <N> · Lane: <lane>
   - URL: <url>
   - JD on disk: job_bodies/<jobid>.txt
   - Why priority: <warm-path reason or lane match>

(repeat for each priority pick)

## STRONG MATCHES (5 to 10 — apply if priority queue clears)

(same format, no priority reason)

## STRETCH (optional — only if you have bandwidth)

(brief one-liners, no full block)

## SKIPPED (transparency, what got filtered)

- <N> already applied
- <N> hard-blocked: <reasons>
- <N> stack mismatch
```

Cap PRIORITY at 5 and STRONG at 10. More than that and Jacob freezes (memory: he overwhelms without concrete action items).

### Step 6 — Offer to tailor

After presenting, ask Jacob which jobs to tailor. He picks by number or "all priority". Then:

1. Confirm each picked job has `job_bodies/<jobid>.txt` already scraped. If missing, tell him to run the scrape step first (do not auto-scrape — that's a separate flow).
2. For tailoring, invoke the existing pipeline:

```bash
cd "/home/z1337/Desktop/PROJECTS/JOB TRACKING" && bash tools/tailor_with_claude.sh --limit <count>
```

This script picks up pending ⏳ rows from APPLY_THESE.md, runs `claude --model opus` per JD, builds 1-page PDFs, and flips rows to 🟢 READY TO APPLY.

3. After it finishes, read APPLY_THESE.md again and confirm which rows are now 🟢 with PDF paths. Print the final list of PDFs ready to attach.

## Output discipline

- Do NOT recommend more than 5 PRIORITY jobs in one week (ADHD overwhelm risk)
- Do NOT include already-applied jobs in any list — only in the SKIPPED summary count
- Do NOT auto-tailor without explicit Jacob confirmation. The tailor script consumes Claude tokens and Chromium PDF time.
- Do NOT auto-run `twice_weekly_rank_apply.py` unless Jacob says yes — it can take a few minutes
- Always show the full PDF path so he can drag-and-drop into LinkedIn Easy Apply

## Hard rules

1. The published resume at `/home/z1337/Desktop/PROJECTS/jacobdcook dot com/assets/resume.html` is source of truth. Never let the tailor add tools/certs Jacob doesn't have. The tailor script enforces this; just confirm the constraint in any handoff prompt.
2. If a JD on disk is < 500 chars, the LinkedIn session was stale during scrape. Flag and do not tailor.
3. Never modify `JOBS_SCORED.xlsx` or `all_scraped_jobs.xlsx` directly. Only the pipeline scripts write to them.
4. Eligible only: roles that are Remote, Hybrid in CA Bay Area / Sacramento, or open to relocation Jacob has agreed to.

## Quick test

To dry-run the skill without tailoring anything:

```bash
cd "/home/z1337/Desktop/PROJECTS/JOB TRACKING" && python3 tools/select_pending_jds.py --limit 30
```

That lists current pending JD paths, which is what STRONG MATCHES should largely overlap with.
