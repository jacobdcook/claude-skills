---
name: daily-morning
description: >-
  Morning brief with news, weather, Davis CA allergy snapshot, agenda from calendar
  or workspace, and recent reminders. Triggers on /daily-morning, "morning brief",
  "what's today look like", or "catch me up".
license: MIT
---

# Daily Morning Brief

Provides a daily morning brief including news, weather, project status, and schedule.

## Usage

```
/daily-morning
```

## Behavior

When invoked, this skill:

1. **News**: Fetches trending or breaking news from the past 24 hours using web search
2. **Weather**: Gets today's weather forecast (defaults to Pacific Time, user's location if known)
3. **Allergy Report**: Checks pollen and allergy levels for Davis, CA (high/moderate/low) with dominant allergens
4. **Today's Agenda**: Checks active project tasks, meetings, and deadlines from project workspace or memory
5. **Recent Context**: Pulls relevant reminders from recent conversations over the past week
6. **Format**: Delivers a well-formatted, concise brief with emojis and clear sections

## Output Format

Sections:
- **News Brief** — 2-3 top stories with 1-line summary each
- **Weather** — Today's forecast with temp range and conditions
- **Allergy Alert** — Davis, CA pollen levels (High/Moderate/Low) with top allergens; window guidance
- **On Your Plate Today** — Active tasks, meetings, deadlines from workspace or calendar
- **Quick Reminder** — Recent context or patterns from past week, motivation
- Motivational one-liner ("zazz")

## Triggers

- User types `/daily-morning`
- User asks "morning brief", "what's today look like", or "catch me up"

---

## Install (Claude Code)

1. Save as `~/.claude/skills/daily-morning/SKILL.md` (folder `daily-morning`).
2. Optional slash command file `~/.claude/commands/daily-morning.md`: one line that tells Claude to read this skill; pass city or notes in `$ARGUMENTS` if you want overrides for weather or allergies.

## Customize

- Default allergy city in the skill text is **Davis, CA**; change to your metro.
- Requires **web search** for news and weather; **Google Calendar MCP** or manual agenda if you wire calendar.
