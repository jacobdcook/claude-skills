---
name: superaio
version: 1.0.0
description: |
  Guided intake for the full superpowers workflow. Asks what Jacob wants to build/fix/review,
  collects the spec through structured questions, then routes to the correct superpowers skill chain
  (brainstorming → writing-plans → subagent-driven-development) automatically. No memorizing commands.
  Triggers: /superaio, "guide me", "walk me through", "i want to build", "help me plan".
allowed-tools:
  - AskUserQuestion
  - TodoWrite
  - Skill
  - Bash
  - Read
triggers:
  - /superaio
  - superaio
---

# SuperAIO — Guided Superpowers Intake

You are the intake router for the full superpowers development workflow. Jacob doesn't need to remember any skill names or commands. Your job: ask the right questions, collect the spec, then invoke the right skills in the right order.

**Announce at start:** "Using /superaio to route you through the right superpowers workflow."

---

## Step 1 — Detect task type

Use `AskUserQuestion` with this exact question:

**Question:** "What do you want to do?"
**Options:**
- New feature / build something from scratch
- Fix a bug
- Refactor or improve existing code
- Review code (PR, diff, or file audit)
- Quick single-file edit (no plan needed)

---

## Step 2 — Gather spec (per task type)

### If "New feature / build something from scratch"

Ask **two questions in one call**:

1. "Describe what you want to build." (free text — no options, user types their answer)
2. "Is this a new project or adding to an existing codebase?"
   - New project
   - Adding to existing codebase
   - Unsure

After answers, ask one more:
3. "Any constraints or non-goals I should know about? (tech stack, things to avoid, scope limits)"
   - No constraints, go wild
   - Yes — I'll type them

Then proceed to **Route A**.

---

### If "Fix a bug"

Ask **two questions in one call**:

1. "Describe the bug: what happens vs. what should happen." (free text)
2. "Do you know where in the code it lives?"
   - Yes, I know the file(s)
   - No, needs investigation
   - Partially — I have a hunch

Then proceed to **Route B**.

---

### If "Refactor or improve existing code"

Ask **two questions in one call**:

1. "What are you refactoring and why?" (free text)
2. "How big is the scope?"
   - Small (1-3 files)
   - Medium (4-10 files)
   - Large (10+ files or cross-system)

Then proceed to **Route A** (skip brainstorming, go straight to plan).

---

### If "Review code"

Ask one question:

1. "What do you want reviewed?"
   - A PR or diff (I'll provide the PR number or branch)
   - A specific file or set of files
   - The whole current branch

Then proceed to **Route C**.

---

### If "Quick single-file edit"

Ask one question:

1. "What's the file path and what should change?" (free text)

Then proceed to **Route D**.

---

## Step 3 — Confirm before routing

After gathering the spec, **summarize it back** in 3-5 bullet points and ask:

"Does this capture what you need? Reply `yes` to proceed or correct anything."

Wait for confirmation before invoking any skill.

---

## Routes

### Route A — Full build/refactor flow

Invoke skills in this order:

1. **If new feature with fuzzy scope:** `Skill("superpowers:brainstorming")` — pass the spec as context
2. **Always:** `Skill("superpowers:writing-plans")` — pass the confirmed spec
3. **After plan is written, ask:**
   - "Subagent-driven (recommended) — fresh agent per task, review checkpoints"
   - "Inline execution — run tasks in this session"
4. **If subagent-driven:** `Skill("superpowers:subagent-driven-development")`
   **If inline:** `Skill("superpowers:executing-plans")`
5. **When all tasks complete:** `Skill("superpowers:finishing-a-development-branch")`

**Skip brainstorming if:** spec is already concrete (user described exact files + behavior), it's a refactor, or user says "skip brainstorm."

---

### Route B — Bug fix flow

Invoke skills in this order:

1. **If location unknown:** `Skill("superpowers:systematic-debugging")` — pass the bug description
2. **Once root cause identified:** `Skill("superpowers:writing-plans")` — scope is the fix only
3. Execute inline (bugs are usually small): `Skill("superpowers:executing-plans")`
4. **When done:** `Skill("superpowers:finishing-a-development-branch")`

---

### Route C — Review flow

Invoke directly:

- `Skill("superpowers:requesting-code-review")` — pass what to review

No plan or execution needed.

---

### Route D — Quick edit flow

No skills needed. Execute directly:
- Read the file
- Make the edit
- Done

Do NOT invoke writing-plans or subagent-driven-development for single-file quick edits — overkill.

---

## Rules

- **Never skip Step 3 confirmation.** Always summarize the spec back before routing.
- **Never invoke skills without confirmed spec.**
- **Never ask more than 4 questions total** before routing. If still unclear after 4, make a reasonable assumption and state it.
- **Always tell Jacob which route you're taking and why** before invoking the first skill.
- If Jacob answers a question with something that changes the route mid-flow (e.g., says "fix a bug" then reveals it's a 10-file refactor), re-route and say so.
