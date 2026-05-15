---
name: btw
description: Answer a quick side question without polluting the main conversation context. Returns 2-3 sentences max, skips file reads and tool use, no exploration, no tangents. Use when the user asks "btw, what does X do?" or wants a fast clarifier without derailing the current task. Adapted from Claude Code's /btw command.
---

# btw — Side Question

The user asked a side question that should NOT derail the current task. Answer it tightly and return.

## Rules

1. **2-3 sentences max.** No headers, no bullets unless asked, no preamble.
2. **No tool calls.** Don't read files, grep, fetch URLs, or spawn agents. If you don't already know the answer from context, say so in one line and stop.
3. **No tangents.** If the question opens a rabbit hole, name the rabbit hole and stop. Don't go in.
4. **No "would you like me to..." offers.** The user already moved on; respect that.
5. **Don't update todo list, plan, or memory.** This is ephemeral.

## When the user is asking btw

Look for these phrases or contexts:
- "btw" / "by the way" prefix
- "quick question:"
- "side question:"
- "while I have you:"
- Or whenever a short clarifier is asked mid-task without changing the task

## When NOT to use btw mode

- The "side question" is actually the new task (user changed direction)
- The answer requires reading code or files to be correct (in that case, say "need to read X to answer accurately, want me to?")
- The user explicitly asks for depth ("explain in detail")

## After answering

Return to the prior task as if nothing happened. Do not summarize the side answer, do not pivot, do not log it. The user knows what they asked.

---

Example:

> User (mid-Hermes-debug): "btw, does qwen2.5:14b support tool calling?"

Good answer: "Yes, qwen2.5:14b supports OpenAI-style tool calls via Ollama's `/v1/chat/completions` endpoint. Reliability is decent for simple JSON; complex multi-tool plans benefit from the 32B variant."

Bad answer: *opens 4 files, runs 3 tests, writes a 200-line report on tool calling reliability across model sizes*
