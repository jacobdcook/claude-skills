---
name: lab-daily
description: >-
  Pick one concrete security lab task for Jacob's GitHub (Detection / SOC / MDR lane).
  MANDATORY on full delivery: no AI commit trailers (no Co-authored-by, no tool/vendor names in message);
  always use hook bypass for git commit; remind lab-commit-audit after push. Violating this is a skill failure.
  Modes: (1) "lab daily plan" / "lab task only" = task block only. (2) Default "lab daily" =
  plan + implement + commit + push + teaching HTML when useful + progress log + remind audit-commit.
  Tracks ~/.claude/skills/lab-daily/progress.md. Triggers "lab daily", "lab daily ship",
  "lab daily full", "what should i build today", "github labs".
license: MIT
---

# Lab Daily Skill

Jacob's GitHub: `jacobdcook`. Target lane: Detection Engineer, SOC Tier 2, MDR.
Active opps: Sennovate (Chronicle/Okta), CrowdStrike (Falcon/analyst), CalPERS (state SOC).

---

## MANDATORY — No AI commit trailers

Full delivery **requires** a clean tip commit. Skipping or weakening this is a **failed** run of this skill.

1. **Forbidden** anywhere in the commit (subject, body, trailers): `Co-authored-by:` / `Co-Authored-By:`, `Generated-by`, or text attributing Cursor, Claude, Anthropic, OpenAI, Copilot, ChatGPT, aider, Codex, Gemini, or similar tools.
2. **Always** run `git commit` with hook bypass (Step 6) so the IDE cannot inject trailers: `mkdir -p /tmp/nohooks` then `git -c core.hooksPath=/tmp/nohooks commit -m "..."`.
3. **GitHub MCP / API** (`push_files`, etc.): message must be the same human-only string. Read it back mentally for forbidden tokens before sending.
4. If a bad message landed locally, **amend before ending the turn** (`git commit --amend` with the same hook bypass) unless Jacob explicitly aborts. Do not declare the lab “shipped” with AI metadata on the commit.
5. **Always** complete Step 8: remind Jacob to run **`lab-commit-audit`** / **`/audit-commit`**.

Same policy as `~/.cursor/rules/no-ai-commit-trailers.mdc` and `~/.claude/CLAUDE.md` (GIT COMMITS section).

---

## Modes

| User says | Agent does |
|-----------|------------|
| **lab daily plan**, **lab task only**, **dry run** | Steps 1 to 4 only (task block). No code, no push. |
| **lab daily**, **lab daily ship**, **lab daily full**, **github labs** (default) | Steps 1 to 4, then **Step 6 Full delivery** (implement, commit, push, HTML if appropriate, progress, audit reminder). |

If ambiguous, assume **full delivery** so Jacob does not need a second message to start building.

---

## Step 1 — Pull repo state via GitHub API

Run and capture output:

```bash
curl -s -H "Authorization: Bearer $GITHUB_TOKEN" \
  "https://api.github.com/users/jacobdcook/repos?per_page=100" | \
  python3 -c "
import sys, json
from datetime import datetime, timezone
repos = json.load(sys.stdin)
now = datetime.now(timezone.utc)
security_repos = [
  'soar-incident-orchestrator','okta-detection-engine','stryker-intune-detection-pack',
  'blue-team-soc-monitoring-lab','Phishing-Analysis-Lab','aws-identity-detection-lab',
  'network-behavior-analyzer','ai-log-auditor','cloud-security-auditor',
  'Azure-Cloud-Hardening-Lab','tcm-soc-101-lab-notes','security-plus-labs'
]
for r in repos:
  if r['name'] in security_repos:
    pushed = datetime.fromisoformat(r['pushed_at'].replace('Z','+00:00'))
    days = (now - pushed).days
    print(f\"{days:3}d ago | {r['name']}\")
" | sort -n
```

If `GITHUB_TOKEN` is unset, use MCP `search_repositories` with query `user:jacobdcook sort:updated` for the same repo list staleness.

---

## Step 2 — Read progress log

Read `~/.claude/skills/lab-daily/progress.md`. Missing file means everything is still open.

---

## Step 3 — Pick today's task

Use the priority list below plus staleness and progress. Skip rows already logged in `progress.md`.

### TIER 1: Incomplete flagship repos
1. **okta-detection-engine** — `tests/` from `sample_events.json`, benign negatives, README verification section and optional screenshot path.
2. **aws-identity-detection-lab** — Entra-shaped fixtures, password spray `T1110.003`, guest-invite risk, pytest.
3. **cloud-security-auditor** — Pure functions, pytest, optional CI with checkov; README not AI-first.
4. **network-behavior-analyzer** — Tests for beaconing math and exfil thresholds from fixtures.
5. **stryker-intune-detection-pack** — `kql/` or `sentinel/` with 3 queries (wipe, policy velocity, Entra privileged + Intune window).
6. **Azure-Cloud-Hardening-Lab** — Detection hook: Defender or NSG flow angle + sample KQL for non-authorized RDP/SSH.

### TIER 2: New repos (one at a time)
7. **entra-password-spray-detection** — Sigma `T1110.003`, KQL, fixtures, pytest, CI.
8. **chronicle-detection-corpus** — UDM samples, field map, YARA-L notes.
9. **cloudtrail-assumerole-lab** — AssumeRole chains, fixtures, Sigma optional.
10. **wazuh-custom-rule-pack** — Decoder + rule, evidence, shell test.

### TIER 3: Maintenance (when Tier 1 to 2 are current)
11. Archive: `cybersecurity-learning-journey`, `security-plus-labs`, `tcm-soc-101-lab-notes`
12. Private: `interview-assistant`, `jobright-automator`, `thm-streak-keeper`, `click-at-time`, `discord-guild-leave-gui`, `meal-planner`, `kokoro-gui`, `daily-wellness-scheduler`, `raspberry-pi-day-planner`
13. Delete: `GioHomePage`, `193aSimpleWebsite`, `fancify-my-text`, `HealthLink`

---

## Step 4 — Output format (always)

Pick ONE task. Print exactly:

```
TODAY'S LAB TASK
----------------
Repo:        <repo name> (last commit: X days ago)
Task:        <one specific concrete thing to build>
Why now:     <one sentence>
Time:        <15 min / 30 min / 1 hr>
Start here:  <first file or command>
```

If **plan-only mode**, stop after this block. Do **not** ask whether to build; Jacob uses `lab daily` when he wants the full pipeline.

---

## Step 5 — Progress log (after work is done)

Append one line only. Never rewrite the file.

Path: `~/.claude/skills/lab-daily/progress.md`

```
- [YYYY-MM-DD] <repo>: <one line>
```

---

## Step 6 — Full delivery (default mode only)

Full delivery must satisfy **MANDATORY — No AI commit trailers** above. Items 5 to 8 below implement that policy.

After the task block, **continue in the same turn** without waiting for confirmation:

1. **Workspace** — Use `~/Desktop/PROJECTS/<repo>` or clone `https://github.com/jacobdcook/<repo>.git` if missing.
2. **Implement** — Smallest change that completes the task. Match existing style. No AI attribution in code comments.
3. **Verify** — Run tests, linters, or smoke commands the repo already uses.
4. **Teaching HTML** — When the change is non-trivial or concept-heavy (detections, Okta, Sigma, KQL), add or update `docs/<short-topic>-refresh.html` in that repo (self-contained, open in browser). Skip for trivial one-line fixes if unnecessary.
5. **Commit** — Message reads like a human developer. **No `Co-authored-by:`.** Use hook bypass so IDEs do not inject trailers:

```bash
mkdir -p /tmp/nohooks
git -c core.hooksPath=/tmp/nohooks commit -m "Short imperative subject"
```

6. **Push** — `git push` from the repo (Jacob’s credentials). If GitHub MCP `push_files` is the only path (no local git), use it with the same clean message and no trailers.
7. **Append** `progress.md` (Step 5).
8. **Remind** — Tell Jacob to run **`/audit-commit`** or **lab audit** (skill `lab-commit-audit`) on that repo to confirm no AI metadata on the new commits.

Do **not** add "Co-Authored-By" lines. Jacob’s global rules forbid them.

---

## Related skill

After shipping, run **`lab-commit-audit`** (`/audit-commit`) to scan `git log` for AI trailers and junk subjects.
