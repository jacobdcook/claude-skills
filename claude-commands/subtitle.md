# /subtitle — YouTube subtitles (.srt)

The user ran **`/subtitle`** with optional arguments. Treat **`$ARGUMENTS`** and the rest of the user message as the input. Find **one** YouTube URL (`youtube.com/watch`, `youtu.be`, or `youtube.com/shorts`). Strip nested `downsub.com/?url=` junk so only the real YouTube URL remains.

## Do this now

1. **Run** (output on Desktop unless the user named another directory):

```bash
cd "$HOME/Desktop" && yt-dlp --write-auto-sub --write-sub --sub-langs "en.*,en" --convert-subs srt --skip-download -o "subtitle_%(id)s.%(ext)s" "YOUTUBE_URL"
```

Replace `YOUTUBE_URL` with the extracted URL. Use `cd` to an absolute path if they asked for a different folder.

2. If English subs are missing or the command errors, run **`yt-dlp --list-subs "YOUTUBE_URL"`**, then retry with an available language code (still `--convert-subs srt`).

3. If **`yt-dlp` is not found**, tell them to install: `pip install yt-dlp` (or their OS package), then re-run.

4. **Reply** with the full path(s) of the `.srt` file(s) created. If they only want text in chat, paste a reasonable excerpt from the file.

Do not open DownSub unless `yt-dlp` cannot get subtitles after listing subs.

Personal use only; respect YouTube’s terms.
