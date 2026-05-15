---
name: debrief
description: >-
  Deep, multi-layer debrief when you share a conversation, transcript, decision,
  or want the model to think hard about what you are saying. Triggers on /debrief, "debrief
  this", "think deeply", "think about what I'm saying", "read between the lines",
  "what am I missing", "help me process this", or when you paste long chat or audio text
  and want analysis, not a short summary.
license: MIT
---

# Debrief (deep thinking)

When this skill applies, **do not optimize for a short reply**. You are asking for **depth**: careful reading, explicit reasoning, and conclusions tied to evidence.

## Install (Claude Code)

1. Save this file as `~/.claude/skills/debrief/SKILL.md` (create the `debrief` folder if needed).
2. Optional slash command: create `~/.claude/commands/debrief.md`:

```markdown
# Debrief

Read and follow `~/.claude/skills/debrief/SKILL.md` (deep multi-layer analysis, not a short summary).

User focus or pasted material: `$ARGUMENTS`

1. If `$ARGUMENTS` is empty, ask what to debrief (paste, file path, or "last call").
2. Otherwise run the full structured output from the skill on that material.
```

## Load context first

1. If the debrief touches **career, job search, networking, interviews, relationships, or strategy**, read `~/.claude/user_profile.md` when that file exists. Fold in only facts that are **consistent with the new material** (do not force old notes to override what you just pasted).
2. If you name a **file path**, read it. If you reference **recall / Pinecone / memory**, run the recall path only when needed for continuity, not instead of reading what you supplied in the message.

## Operating mode

- **Primary evidence** is always what you supplied in this turn (and any files you pointed to). Build from that first.
- **Slow down**: trace logical steps in your own reasoning. Prefer **one strong chain of inference** over many shallow bullets.
- **Label uncertainty**: for anything not spelled out in the source, mark it as inference, hypothesis, or guess.

## Required output structure

Deliver in **this order** (headings ok, but skip empty sections only if truly N/A):

1. **Surface read** — What happened, in plain language. Quotes or timestamps only when they anchor a non-obvious point.
2. **Stakes** — Why this conversation or moment matters for your goals (job, relationships, reputation, energy). Be specific.
3. **Their frame** — What the other person(s) likely wanted, feared, or were signaling. Separate **evidence** from **inference**.
4. **Your frame** — What you were signaling, avoiding, or over-indexing on. Name **contradictions** if your words and goals do not line up.
5. **Subtext / emotion** — Anxiety, pride, rescue fantasies, defensiveness, hunger to be seen, etc. Tie each line to the text.
6. **Dynamics** — Rapport, trust, status, talk-time balance, demo-vs-discovery balance, who was doing the work of the conversation.
7. **Gaps and silence** — Questions not asked, offers not made, threads dropped, risks unacknowledged.
8. **Patterns** — Echoes of prior behavior you have named before (e.g. demo-first, over-explaining, shrinking in the room). One pattern beats five buzzwords.
9. **Risks** — Misread, reputational, or strategic downside if you repeat this approach.
10. **Leverage** — What is genuinely working and should be **kept** or **doubled**.
11. **Moves** — **3 to 7** concrete next actions (who, what, by when). No vague "follow up."
12. **Single take** — **One paragraph**: if you remember nothing else, remember this.

## Depth rules

- **No throat-clearing** (no "Great question", no long preamble).
- **No em dash** (Unicode U+2014) in prose you mean to copy out; use commas, periods, or "to".
- **No invented dialogue**. Paraphrase from the source; do not quote people unless those words appear in what you provided.
- **Do not flatten** into generic advice. If the debrief could apply unchanged to a stranger, rewrite until it is **obviously about your material**.
- If the source is **messy ASR** (Whisper), call out **low-confidence lines** when meaning changes the analysis.

## Length

- Default: **long enough to honor the request**. If the paste is long, allow a **proportionally deep** response unless you cap length.
- If you say **"short debrief"**, compress to: Surface, Their frame, Your frame, Moves (3), Single take.

## After the debrief

If the session surfaced **durable facts** (new contact, commitment, deadline, strategic shift), update `~/.claude/user_profile.md` (or your own profile note) in the same session when you use one.
