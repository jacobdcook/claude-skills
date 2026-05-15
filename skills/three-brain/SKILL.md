Use this skill whenever the user asks to route work across multiple models, requests a second opinion on work Claude just produced, asks for adversarial review, asks to hand off a stuck task, or references "three-brain".

Primary instruction source (read in order):

1. **`three-brain.md` in this same directory** as `SKILL.md` (the full router spec shipped with this skill).
2. If that file is missing (legacy install only), fall back to: `/home/z1337/Desktop/PROJECTS/three-brain.md`

Execution policy:

- Treat the loaded file as the canonical router spec.
- Apply its MUST-FIRE and DO-NOT-FIRE conditions.
- Enforce its no-self-review rule.
- Use its required routing, announcement, and logging behavior.
- If the file changes, always follow the newest version.
