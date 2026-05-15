---
name: subtitle
description: >-
  One-shot YouTube subtitles. Primary: /subtitle slash command. User types /subtitle plus YouTube URL;
  agent runs yt-dlp, saves .srt to ~/Desktop, prints path. Also triggers on "subtitle" plus link without slash.
license: MIT
---

# /subtitle — YouTube subtitles (one shot)

## Primary entry (use this)

| Tool | Slash command file |
|------|-------------------|
| **Claude Code** | `~/.claude/commands/subtitle.md` → type **`/subtitle`** then the YouTube URL |
| **Cursor** | `~/.cursor/commands/subtitle.md` → type **`/subtitle`** then the YouTube URL |

The command body and this skill say the same thing: extract one YouTube URL, run **`yt-dlp`**, save **`.srt`** on **Desktop** unless another path is given.

Claude Code also passes **`$ARGUMENTS`** into the command; use that plus the visible user message for the URL.

Without slash: messages like **`subtitle https://youtu.be/...`** still match this skill.

---

## Agent: do this

1. Parse the **one** canonical YouTube URL (strip nested `downsub.com/?url=` if present).
2. Output dir: **`~/Desktop`** unless Jacob named another path.
3. Run (install `yt-dlp` if needed):

```bash
cd "$HOME/Desktop" && yt-dlp --write-auto-sub --write-sub --sub-langs "en.*,en" --convert-subs srt --skip-download -o "subtitle_%(id)s.%(ext)s" "YOUTUBE_URL_HERE"
```

If English fails: `yt-dlp --list-subs "YOUTUBE_URL_HERE"` then retry with an available language.

4. **Report** full path to `.srt` file(s), or paste an excerpt if they want text only.

---

## Optional: DownSub

Only if `yt-dlp` cannot get tracks after listing subs: browser at `https://downsub.com/?url=` + encoded URL, click **RAW**.

---

## Notes

- Personal study or allowed reuse only.
