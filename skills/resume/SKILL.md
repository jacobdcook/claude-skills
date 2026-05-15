---
name: resume
description: >-
  Tailor Jacob Cook's resume for a specific job description. Reads base resume.html,
  classifies the role lane (entry-soc / detection-engineer / ir-soc-ii / risk-grc),
  tailors headline + summary + skills + projects, generates PDF, verifies 1 page.
  Triggers on "/resume", "tailor my resume", or when user pastes a JD after /resume.
  Outputs file:// URL ready to copy. Works across Claude Code, Cursor, and Antigravity.
---

# /resume — Resume Tailor

Jacob pastes a job description (or a URL to scrape) after `/resume`. Your job is to produce a tailored, one-page PDF-verified resume, lane-classified, with no fabricated credentials.

---

## Paths (absolute — never assume cwd)

| Item | Path |
|------|------|
| Base resume | `/home/z1337/Desktop/PROJECTS/jacobdcook dot com/assets/resume.html` |
| Output folder | `/home/z1337/Desktop/PROJECTS/jacobdcook dot com/applying/` |
| Output filename | `Jacob_Cook_Resume_<Company>_<Role>.html` (no spaces — use underscores) |

---

## Step 1 — Get the JD

If the user passed a URL instead of raw text, scrape it first:
```bash
curl -sL "<URL>" | python3 -c "import sys,html,re; t=sys.stdin.read(); t=re.sub('<[^>]+>','',t); print(html.unescape(t)[:6000])"
```
If raw text was pasted, use it directly. If nothing was provided after `/resume`, ask: "Paste the job description or drop a URL."

---

## Step 2 — Read the base resume

```bash
cat "/home/z1337/Desktop/PROJECTS/jacobdcook dot com/assets/resume.html"
```

Do this before any edits. The base is the source of truth for structure, CSS, and qualifications.

---

## Step 3 — Classify the lane (CRITICAL)

Pick exactly ONE lane. Print `LANE: [x]` before continuing.

| Lane | Trigger signals |
|------|----------------|
| `entry-soc` | Title: SOC Analyst I, Tier 1, Junior, Jr., Associate, Entry, Info Sec Analyst, Security Analyst I, Cybersecurity Analyst I — OR required YOE 0-2 |
| `detection-engineer` | Title: Detection Engineer, Threat Hunter, Security Automation, SOAR Engineer — OR YOE 2-5 AND JD explicitly names Sigma/KQL/detection-as-code |
| `ir-soc-ii` | Title: Incident Response, SOC Analyst II, Tier 2, Cyber Incident Responder, Security Operations Engineer — OR YOE 2-3 with IR emphasis |
| `risk-grc` | Title: Risk Analyst, Compliance Analyst, GRC, Security Compliance, Audit |

---

## Step 4 — Tailor the resume

Copy base → output file, then edit only these four regions. Never hand-write HTML from scratch.

### Headline (`class="headline"`)
- Format: `[Lane prefix] | [top 4-5 JD skills separated by dots]`
- Hard cap: ≤ ~100 characters (long headlines wrap to page 2)
- Do NOT prefix with "Cybersecurity" as a redundant word

Lane prefixes:
- `entry-soc` → `Security Analyst` or `SOC Analyst` (match JD exactly — never "Detection Engineer" here)
- `detection-engineer` → `Detection Engineer` or exact JD title
- `ir-soc-ii` → exact JD title
- `risk-grc` → exact JD title

### Summary (`class="summary"`)
- Start with the lane opener (hardcoded below), then 1-2 sentences mirroring JD language
- Hard cap: ≤ ~720 characters

Lane openers:
- `entry-soc`: `Security analyst with hands-on SIEM, detection triage, and incident documentation experience from lab builds, certifications, and six years of operations monitoring that translates directly into Tier 1 and Tier 2 SOC work.`
- `detection-engineer`: `Detection-focused security practitioner with hands-on experience building SIEM environments, engineering detection rules, and automating incident response workflows.`
- `ir-soc-ii`: `Incident response-focused security practitioner with hands-on SIEM build, alert triage, and automated playbook experience from lab projects, plus six years of operations incident coordination at scale.`
- `risk-grc`: `Security practitioner with CompTIA credentials spanning offensive and defensive disciplines, hands-on SIEM and automation lab builds, and six years of audit-tracked incident documentation that maps to risk and compliance workflows.`

### Technical Skills (`.skill-row` lines)
- Reorder so JD-relevant skills appear first
- Each row ≤ ~130 characters (longer wraps to two lines and pushes overflow)
- Add JD tools as `(familiar)` ONLY if already in the master resume — never add forbidden tools

**First row lead terms by lane:**
- `entry-soc`: `SIEM (Wazuh), Alert Triage, Phishing Analysis, Incident Documentation, IOC Analysis`
- `detection-engineer`: `Threat Detection, Detection as Code (Sigma), SIEM (Wazuh), Playbook Automation, SOAR`
- `ir-soc-ii`: `Incident Response, Alert Triage, SIEM (Wazuh), Playbook Automation, IOC Analysis`
- `risk-grc`: `Risk Assessment, Security Controls, Audit Documentation, Incident Documentation, Compliance Frameworks`

### Projects (reorder by JD relevance)
Default lead project when JD doesn't name a specific tool match:
- `entry-soc` → `Blue Team SOC Monitoring Lab: Wazuh SIEM`
- `detection-engineer` → `Stryker / Intune Detection Pack: Detection-as-Code`
- `ir-soc-ii` → `SOAR-lite: Incident Response Orchestrator`
- `risk-grc` → `Blue Team SOC Monitoring Lab: Wazuh SIEM` (emphasize documentation/audit angle)

Echo JD terminology in bullet wording (if JD says "alert triage" use "alert triage" not "alert investigation").

---

## Step 5 — Generate PDF and verify 1 page

```bash
cd "/home/z1337/Desktop/PROJECTS/jacobdcook dot com/applying" && \
chromium --headless --disable-gpu \
  --print-to-pdf="Jacob_Cook_Resume_<Company>_<Role>.pdf" \
  --no-margins \
  "Jacob_Cook_Resume_<Company>_<Role>.html" 2>/dev/null && \
pdfinfo "Jacob_Cook_Resume_<Company>_<Role>.pdf" | grep Pages
```

**If Pages: 2** — trim in this order until it fits:
1. Shorten summary to 2-3 lines
2. Remove lowest-relevance project
3. Combine similar skill items
4. Trim bullet points to 1 line each
5. Last resort: reduce font size 9.75pt → 9.5pt

**If Pages: 1 but visually short (~under 85% of a full letter page)** — add content back:
- Second/third factual bullets on top projects (metrics, scope, tests, tools)
- Okta detection engine or Azure hardening lab line if JD-relevant
- Another Target bullet tied to the JD

Re-run PDF generation and confirm `Pages: 1` before declaring done.

---

## Step 6 — Output

Print both paths in this format:

**HTML:**
```
file:///home/z1337/Desktop/PROJECTS/jacobdcook%20dot%20com/applying/Jacob_Cook_Resume_<Company>_<Role>.html
```

**PDF:**
```
file:///home/z1337/Desktop/PROJECTS/jacobdcook%20dot%20com/applying/Jacob_Cook_Resume_<Company>_<Role>.pdf
```

Always use `%20` encoding for the space in `jacobdcook dot com`. Never use `~/...` or bare filesystem paths.

---

## Fabrication lock (non-negotiable)

The master `resume.html` is the source of truth. Never add tools, certs, or employers Jacob does not have.

**OK to emphasize:** Wazuh, Azure AD, Okta, Microsoft Sentinel (familiar), Terraform, FastAPI, React, Python, Bash, SQL, Git, Linux

**Never add as his stack:** Splunk, CrowdStrike, Falcon, SentinelOne, Velociraptor, Tenable, Rapid7, Qualys, Tanium, KAPE, Volatility, GCFA, GCIH, GREM, OSCP, PNPT, CISSP, CEH, AWS cert, GCP cert, Snowflake, BigQuery, Elasticsearch, Kibana, Tines, XSOAR, Cortex, Jamf, Go/Golang, Ruby, Rust

**Certs Jacob actually has:** Security+, CySA+, PenTest+, SecurityX (CSIE path)

**Projects Jacob actually has:** Wazuh SOC lab, SOAR-lite IR, Stryker/Intune detection pack, Okta detection engine, Azure hardening lab, G3-GPT platform, Target operations engineering (6 years)

---

## Style rules

- No em dashes or en dashes anywhere in output or drafted resume text (ASCII hyphen only)
- No AI commit trailers if any git commands are run
- Use absolute `/home/z1337/...` paths in all bash commands
