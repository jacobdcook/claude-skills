---
name: job-hunt-flow
description: Run Jacob's JOB TRACKING LinkedIn scrape-to-apply pipeline. Step 1 card scrape to search_results.json, Step 2 audit raw cards and shortlist enriched URLs, Step 3 re-audit job_bodies with Strong/Good/Stretch/Skip, Step 4 tailor HTML resumes and keep APPLY_THESE.md in sync (mandatory rows for Strong+Good). Trigger on "audit", "re-audit", "yes resumes", "job hunt flow", "run the scrape pipeline", "linkedin scrape then audit", or when walking the scrape to tailored resume path.
license: MIT
---

# Job Hunt Flow Skill

Uses the proven `JOB TRACKING` pipeline (the one the cron used). 4-step flow:

1. Scrape (cards-only) → `search_results.json`
2. Audit → tell user which to enrich
3. Enrich (full JD text dump) → `job_bodies/<id>.txt`
4. Re-audit → `APPLY_THESE.md` → tailored resumes

**Note:** This path starts from fresh `search_results.json` plus `tools/scrape_live_jobs.py`. For spreadsheet-only triage (`JOBS_SCORED.xlsx` / rankings), use the `apply-this-week` skill instead.

---

## Step 1 — Scrape

**DEFAULT: always use visible browser (`--headed`).** More human, less likely to trigger bot detection. Only use `--headless` if Jacob explicitly says "headless" or "screen off".

Tell the user to run this. Default session is `emailsformeeee`. `LINKEDIN_SEARCH_MAX_PAGES=15` targets ~80+ clean jobs per run. Runs in roughly 2-3 hours.

**Default (visible browser):**

```bash
cd "/home/z1337/Desktop/PROJECTS/JOB TRACKING" && LINKEDIN_SESSION="/home/z1337/Desktop/PROJECTS/JOB TRACKING/session_emailsformeeee.json" LINKEDIN_SEARCH_MAX_PAGES=15 .venv/bin/python search_jobs.py --headed --builtin-searches
```

**Headless (only if Jacob says screen will be off):**

```bash
cd "/home/z1337/Desktop/PROJECTS/JOB TRACKING" && LINKEDIN_SESSION="/home/z1337/Desktop/PROJECTS/JOB TRACKING/session_emailsformeeee.json" LINKEDIN_SEARCH_MAX_PAGES=15 .venv/bin/python search_jobs.py --headless --builtin-searches
```

Output: `/home/z1337/Desktop/PROJECTS/JOB TRACKING/search_results.json`

After giving the command, say:

> "Run that. When it finishes, come back and say **audit**."

---

## Step 2 — Audit Raw Cards (triggered by user saying "audit")

1. Read `/home/z1337/Desktop/PROJECTS/JOB TRACKING/search_results.json`
2. Read `/home/z1337/Desktop/PROJECTS/JOB TRACKING/APPLY_THESE.md` — extract URLs already marked APPLIED or in the table
3. **Pre-filter (remove before showing anything):**
   - Drop any job where `company` contains (case-insensitive): `DataAnnotation`, `Data Annotation`, `Outlier`, `Remotasks`, `Appen`, `Scale AI`, `Telus International` — these are AI training data farms that spam LinkedIn with fake listings
   - Drop duplicates (same URL already in APPLY_THESE.md)
   - Never mention how many were dropped — just silently remove them
4. Group remaining into tiers:
   - **Tier 1 - Primary lane**: Detection Engineer, Security Operations, SOC Analyst, MDR, SOAR, Threat Hunter, Vulnerability Management, DevSecOps, AppSec
   - **Tier 2 - MSSP entry**: Security Analyst, Cybersecurity Analyst
   - **Tier 3 - IT bridge**: Systems Administrator, IT Support Analyst, Help Desk Analyst, IT Analyst — only surface if Tier 1+2 < 8
5. Output table: `| # | Title | Company | Location | URL | Tier |`
6. Recommend 10-15 to enrich

Then write the shortlisted URLs to a file the enricher uses. The enricher reads from `JOB_URL_STATUS.md`. To bypass that, write a custom shortlist file at `/home/z1337/Desktop/PROJECTS/JOB TRACKING/shortlist_urls.txt` (one URL per line) and tell user:

```bash
cd "/home/z1337/Desktop/PROJECTS/JOB TRACKING" && LINKEDIN_SESSION="/home/z1337/Desktop/PROJECTS/JOB TRACKING/session_emailsformeeee.json" .venv/bin/python tools/scrape_live_jobs.py --only-source shortlist_urls.txt
```

Say:

> "Run that to dump full descriptions for these [N] jobs. Then say **re-audit**."

---

## Step 3 — Re-Audit Enriched Jobs (triggered by "re-audit")

1. Read text dumps from `/home/z1337/Desktop/PROJECTS/JOB TRACKING/job_bodies/<id>.txt` for each shortlisted URL (extract id from URL `/jobs/view/<id>/`)
2. Read `APPLY_THESE.md` — re-filter applied
3. For each job, score against Jacob:
   - **Strong**: detection engineering, Wazuh, Sigma, SIEM, Azure/Entra, Okta, KQL, Python, Terraform, MITRE ATT&CK, SOC Tier 2, MDR, cloud security, identity, IR
   - **Cert match**: Sec+, CySA+, PenTest+ (plus SecurityX where relevant)
   - **Experience match**: 0-2 years, no clearance required
   - **Red flags**: requires CISSP/OSCP/GCIH+5yr, pure help desk, pure compliance
4. Output: `| # | Score | Title | Company | Location | Salary | Why Apply |`
   - Score: Strong / Good / Stretch / Skip
5. Ask:

> "Want tailored resumes for the Strong + Good matches? Say **yes resumes**."

---

## Step 4 — Resume Generation (triggered by "yes resumes")

Resume project directory (literal folder name): `/home/z1337/Desktop/PROJECTS/jacobdcook dot com/`

**Tracking file (mandatory):** `/home/z1337/Desktop/PROJECTS/JOB TRACKING/APPLY_THESE.md`

Do **not** finish a resume batch with files only on disk. Every Strong + Good target must appear in `APPLY_THESE.md` so Jacob has one ledger for submissions.

### APPLY_THESE.md rules for this skill

1. **When Jacob says `yes resumes` (batch start):** Add a new subsection under `## RESUMES BUILT (ready to submit)` (for example `### Batch YYYY-MM-DD — Strong + Good (tailored resumes)`) with a table row for **each** Strong + Good role **before** tailoring begins. Use status `🟢 BUILDING` or `⏳ TODO` plus company, role, and `[Apply](https://www.linkedin.com/jobs/view/<id>)` when the URL is known (from `re-audit` output, `shortlist_urls.txt`, `JOB_URL_STATUS.md`, or `job_bodies/<id>.txt`). If URL is unknown, use `URL TBD — search LinkedIn`. Leave Tailored HTML and PDF blank or `(pending)` until built.
2. **Immediately after each HTML + PDF is verified:** Update **that row in place**. Set status to `🟢 READY TO APPLY`. Fill Tailored HTML with a markdown link to the absolute path of the dated file under `applying/YYYY-MM-DD/` (percent-encode spaces: `jacobdcook%20dot%20com`). Fill PDF column with the generated `.pdf` filename.
3. **Deduplication:** If the LinkedIn job id already appears anywhere in `APPLY_THESE.md`, update that row instead of adding a duplicate.
4. **After actual submission:** Jacob (or Jacob saying `applied`) updates the status to `✅ APPLIED`. The skill does **not** require a yes/skip ping per resume for table updates; table updates are automatic once the artifact exists.

For each Strong/Good job, one at a time:

1. Read master resume: `/home/z1337/Desktop/PROJECTS/jacobdcook dot com/assets/resume.html`
2. Tailor ONLY:
   - `.headline` (≤100 chars)
   - `.summary` (≤720 chars)
   - Skill-row ordering and keyword substitutions (each row ≤130 chars)
   - Optionally reorder projects
3. Save to: `/home/z1337/Desktop/PROJECTS/jacobdcook dot com/applying/YYYY-MM-DD/Jacob_Cook_Resume_<Company>_<Role>.html`
4. Show diff before saving (or summarize changed fields if diff is noisy)
5. Export PDF — verify exactly 1 page (`pdfinfo` → Pages: 1)
6. **Update `APPLY_THESE.md` for that row** (see rules above).

**FABRICATION RULE**: never add tools/certs Jacob doesn't have.

- Allowed: Wazuh, Azure AD, Okta, Sentinel, Terraform, FastAPI, React, Python, Bash, SQL, Git, Sec+/CySA+/PenTest+/SecurityX
- Forbidden: Splunk, CrowdStrike, Falcon, SentinelOne, Velociraptor, Tenable, Rapid7, Qualys, Tanium, KAPE, Volatility, GCFA, GCIH, GREM, OSCP, PNPT, CISSP, CEH, AWS cert, GCP cert, Snowflake, BigQuery, Elasticsearch, Kibana, Tines, XSOAR, Cortex, Jamf, Go/Golang, Ruby, Rust

Do not claim **CASP+** unless Jacob explicitly states he holds it (CSAP stack is Sec+/CySA+/PenTest+, not CASP+).

Style: no em/en dashes, ASCII hyphens only.

---

## Deduplication Rule

At every step, cross-reference `APPLY_THESE.md` URLs. Never resurface a job already on the table.
