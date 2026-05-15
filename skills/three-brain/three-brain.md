---
name: three-brain
description: |
  Auto-routes work to GPT-5.5 (Codex) or Gemini 2.5 Pro when the task fits
  their unique capabilities better than Claude alone. Claude stays the driver —
  the others are tools it calls.
---

# HARD RULE — THE NO-SELF-REVIEW LAW (READ THIS FIRST)

When the user asks Claude to "check / review / look over / proof / verify /
audit / sanity-check / second-opinion" ANY work Claude (you) just produced —
code, writing, plan, design, edit, anything — this is a MUST-FIRE situation.
You MUST route to Codex.

Claude reviewing Claude's own output is the exact failure mode this skill exists
to prevent. Same architecture = same blind spots. A self-review catches nothing
meaningful and defeats the entire purpose of the three-brain stack.

Phrases that MUST trigger Codex review (not exhaustive):

- "check over your work"
- "check your work"
- "review what you just did"
- "review your code"
- "review the code you wrote"
- "look over this"
- "give it a once-over"
- "go over what you wrote"
- "is this right?"
- "is the code right?"
- "anything wrong with this?"
- "second opinion"
- "sanity check"
- "double-check"
- "proof this"
- "audit this"
- "verify this"
- "make sure this works"

Do NOT silently self-review.
Do NOT say "I'll review it inline."

Route to Codex via Bash:

```bash
git diff | codex exec --skip-git-repo-check "Review this. Find bugs, risks, missing tests."
```

Or for un-tracked code, pipe the file content directly.

After Codex returns, integrate findings into your reply.

State at the end:

```text
(Routed via three-brain → Codex review.)
```

---

# FIRE THIS SKILL WHEN (full list)

## MUST-FIRE

- User asks to review/check/look-over ANY work Claude just produced
  → Codex review

- User says:
  "tear apart / sanity check / stress test / find what's wrong /
  break this / poke holes"
  → Codex adversarial review

- User says:
  "I'm stuck / can't figure this out / hand it off /
  try GPT or Claude has failed the same operation 2+ times in a row"
  → Codex rescue

- Edit touches risky file paths:

```text
src/auth/**
src/billing/**
**/migrations/**
**/deploy/**
**/.env
**/secrets/**
**/policy/**
infra/**
payment/
```

→ Plaid integrations / forced Codex adversarial review BEFORE saving

- User content contains:
  video (.mp4 .mov .webm .avi .mkv),
  audio (.wav .mp3 .flac .m4a .ogg),
  PDF >50pg,
  or YouTube URL
  → Gemini multimodal

- User says:
  "scan the whole repo / find every place X /
  pattern across the codebase / map the architecture"
  → Gemini in-context whole-repo scan

- User explicitly invokes high-stakes consensus mode:
  "ask all three / get cross-architecture consensus /
  before I commit to this"
  → all three in parallel with structured output

---

# DO NOT FIRE FOR

- "explain / what is / how does / walk me through"
  → Claude direct

- "write / draft / create / build / make"
  on non-risky paths
  → Claude direct

- "edit / change / update / refactor"
  on non-risky paths
  → Claude direct

- brainstorming / outline / design
  on non-risky paths
  → Claude direct

- "review my draft email for tone"
  (user's content, not Claude's code)
  → Claude direct

- "look up what we decided about pricing"
  → recall skill, not three-brain

---

# POSITIVE EXAMPLES (MUST FIRE)

```text
"yo check over your work"
→ Codex review (MUST-FIRE)

"review the code you just wrote"
→ Codex review (MUST-FIRE)

"is this right"
(after Claude wrote code)
→ Codex review (MUST-FIRE)

"tear this apart"
→ Codex adversarial review

"is the auth flow right?"
→ Codex adversarial review
(auth path = forced)

"watch this clip and tell me what's interesting"
→ video → Gemini

"i've tried this 3 times, you're not getting it"
→ Codex rescue

"find every place we hit the Stripe API"
→ Gemini repo scan
```

---

# NEGATIVE EXAMPLES (STAY ASLEEP)

```text
"explain how MCP servers work"
→ Claude

"refactor this CSS variable"
(non-risky path)
→ Claude

"write me a tweet about X"
→ Claude

"review my draft email for tone"
(user's content, not Claude's code)
→ Claude

"look up what we decided about pricing"
→ recall skill, not three-brain
```

---

# Three-Brain Auto-Router

A single skill that routes work invisibly.

Three brains, one terminal:

- Claude
  → builder, driver, IDE harness
  → stays in front of the user the whole time

- Codex (GPT-5.5)
  → reviewer, second brain, rescue

- Gemini 2.5 Pro
  → eyes, ears, long-context
  → handles anything Claude literally cannot see or hear

---

# Startup self-check
(run once per session, before first route)

Before the first route fires in a session, verify the tooling.

If anything's missing:
- announce it once
- fall back gracefully
- don't retry every turn

```bash
codex --version 2>&1 | head -1
# expect: codex-cli 0.125+

gemini --version 2>&1 | head -1
# expect: 0.39+
```

If `codex` is missing:

```text
Codex routes unavailable.
Tell the user:
"Codex CLI not found — review/rescue routes off until installed."
Continue without those routes.
```

If `gemini` is missing:

```text
Gemini routes unavailable.
Tell the user the same way.
Continue without those routes.
```

---

# Announcement protocol
(REQUIRED for all forced routes)

If the skill fires a route the user did NOT explicitly request
(i.e. risk-path detection or failure-counter rescue)

announce it in ONE line BEFORE routing.

The user must be able to interrupt with one word.

Examples:

```text
Routing to Codex (adversarial-review)
→ risk path: src/auth/

Routing to Gemini (multimodal scan)
→ video input detected

Routing to Codex (rescue)
→ Claude failed same test 2x in a row
```

---

# Risk-path detection
(path-based, not keyword-based)

```text
src/auth/**        # authentication flows
src/billing/**     # payments, subscriptions
**/migrations/**   # DB schema changes
**/deploy/**       # deployment scripts
**/.env            # env handling
**/secrets/**      # credentials
**/policy/**       # ACLs, permissions
infra/**           # Terraform, k8s, IaC
**/stripe/**       # payment integrations
**/jwt/**          # token handling
```

Keywords alone
(auth, payments, prod)
in casual chat do NOT trigger forced review.

It has to be an active edit on the file paths.

This avoids false positives on docs/comments/test fixtures,
and false negatives on code that uses domain names like
billingFlow or policy.ts instead of generic words.

---

# Risk-by-reversibility rule

Even task verbs that normally don't fire
(refactor/plan/explain/design)

DO fire if the target is irreversible or high-blast-radius:

```text
"Refactor the auth middleware"
→ fires Codex review (auth path)

"Plan the Stripe migration"
→ fires Codex review (billing + migration path)

"Design the deployment pipeline"
→ fires Codex review (deploy path)
```

Verb is irrelevant if the target is risky.

---

# Parallel consensus mode
(high-stakes)

Only when explicitly invoked:

```text
"ask all three"
"before I commit to this"
"cross-architecture consensus"
```

All three answer the SAME question.

Claude does NOT summarize a vibe-consensus.

It forces structured output:

```text
Recommendation:
<one line>

Main risks:
<bullet list>

Disagreements:
<bullet list>

Confidence:
low / medium / high

What must be verified:
<bullet list>
```

Claude returns this template.

Claude diffs:
- where they agree
- where they disagree
- adjudicates by evidence
- NOT by averaging

---

# Logging

Everything the skill produces goes to:

```text
./three-brain-out/YYYY-MM-DD-<slug>/
```

Example:

```text
three-brain-out/2026-04-27-alex-graphql/

prompt.txt
# what the user asked

gemini-analysis.md
# Gemini's output

claude-build.html
# what Claude built

codex-review.md
# Codex's review

summary.md
# one-line summary, dated
```
