---
description: Summarise each WL video, auto-file to playlists, and clear Watch Later
argument-hint: (no arguments — reads /tmp/yt_transcripts.json and /tmp/yt_wl_videos.json)
---

# YouTube Digest — Summarise and File

**Workflow ID**: $WORKFLOW_ID

---

## Load source data

```bash
cat /tmp/yt_transcripts.json 2>/dev/null || echo "TRANSCRIPTS_UNAVAILABLE"
cat /tmp/yt_wl_videos.json
cat /tmp/yt_titles.json
```

---

## Step 1 — Summarise each video

For each video, generate:
- **5–7 bullet points** covering key insights, techniques, or takeaways
- If transcript is null or unavailable: use title-only summarisation ("Based on title: [title], this appears to be about...")
- Assign to **exactly ONE playlist** from this list:
  - Anthropic / Claude
  - AI Agents & Agent Development
  - Google Gemini & AI Tools
  - MCP
  - NotebookLM
  - Python
  - Web Development & Frontend
  - OpenAI / GPT
  - LangChain / LangGraph
  - Skiing
  - Productivity Tools
  - Finance & Trading
  - None of the above → keep in New (do not file)

Save summaries to `/tmp/yt_summaries.json`:
```json
[
  {
    "video_id": "...",
    "title": "...",
    "bullets": ["• ...", "• ..."],
    "playlist": "Playlist Name or 'New'"
  }
]
```

## Step 2 — Auto-file to playlists (Composio)

**Playlist ID reference** (fetch fresh via `YOUTUBE_LIST_USER_PLAYLISTS` if needed):
- Anthropic / Claude → `PL3LVC6eXowNx7VrCQJHmQpYLrf6R82mjZ`
- AI Agents & Agent Development → `PL3LVC6eXowNxaxTJaRb-6BvArI4KmnOD_`
- Google Gemini & AI Tools → `PL3LVC6eXowNzuvXsZYA4mz73QVIT2OUbl`
- MCP → `PL3LVC6eXowNyL0Y6UYLIQ6z27nKaGHppg`
- NotebookLM → `PL3LVC6eXowNwR4YXCA9z3f149TjIoXPXZ`
- Python → `PL3LVC6eXowNwzIQFsvLY7hGEk5PwRWEUx`
- Web Development & Frontend → `PL3LVC6eXowNxZtZ8HCkNa9AccJaDC7H9T`
- OpenAI / GPT → `PL3LVC6eXowNyCHkdf0Yo1qgK-RQQ63dYJ`
- LangChain / LangGraph → `PL3LVC6eXowNzV17uOIW6eXGzwg1_9ywbl`
- Skiing → `PL3LVC6eXowNyEqdYUAYE3iQYG2DgitQFk`
- Productivity Tools → `PL3LVC6eXowNwk6q1Ml41bFGSpygsqSeVR`
- Finance & Trading → `PL3LVC6eXowNwFK5jz4ssKJQwcE94IUUpi`
- New → `PL3LVC6eXowNyoZuYL2KuN6CvHpYUFhCrr`

Use `YOUTUBE_ADD_VIDEO_TO_PLAYLIST` **sequentially** (no parallel calls — causes 409s). Wait 0.3s between each. Treat 409 SERVICE_UNAVAILABLE as success (already in playlist).

After adding, remove filed videos from the "New" playlist: fetch current New playlist items, delete the ones just filed. Fetch fresh item IDs first (stale IDs cause 404s after a batch add).

## Step 3 — Clear Watch Later (mandatory)

**Try API first** (`YOUTUBE_LIST_PLAYLIST_ITEMS` with `playlistId: "LL"`):
- Paginate to find today's video IDs
- Delete each via `YOUTUBE_DELETE_PLAYLIST_ITEM`
- If API returns 0 items (cache lag — common for videos added <2h ago), note it but continue

**If API deletion count < total videos**: fall back to Comet AppleScript removal (pattern in the skill). If both fail, log the failure but never skip the report.

## Output

Report:
- N videos summarised
- N filed to playlists (breakdown by playlist)
- N removed from Watch Later (+ method used: API or Comet)
- Any failures
