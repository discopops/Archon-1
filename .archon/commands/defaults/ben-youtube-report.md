---
description: Post the YouTube daily digest report to Slack and save to Vault
argument-hint: (no arguments — reads /tmp/yt_summaries.json)
---

# YouTube Digest — Report

**Workflow ID**: $WORKFLOW_ID

---

## Load

```bash
cat /tmp/yt_summaries.json
```

---

## Format and post to Slack

Post to `#all-discopops` using Composio `SLACK_SEND_MESSAGE`.

**Format (NO markdown tables):**

```
📺 YouTube Daily Digest — [Day DD Month YYYY]

1. <https://www.youtube.com/watch?v=VIDEO_ID|Video Title Here>
📁 → [Playlist Name]
• [bullet 1]
• [bullet 2]
• [bullet 3]
• [bullet 4]
• [bullet 5]

[repeat for each video]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Completion Summary
• WL videos found: N
• Transcripts fetched: N/N ✅ (or ⚠️ N/N — fell back to title-only)
• Videos summarised: N/N ✅
• Auto-filed to playlists: N/N ✅
• Removed from New after filing: N/N ✅
• Watch Later cleared: [N remaining (verified) ✅ OR ❌ X/N removed — reason]
```

If `/tmp/yt_summaries.json` is missing or empty: post `📺 YouTube Daily Digest — no new videos in Watch Later today.`

## Save to Vault

```bash
REPORT_DATE=$(date '+%Y-%m-%d')
REPORT_DIR=~/Vault/Claude-Code/01-analysis/youtube
mkdir -p "$REPORT_DIR"
```

Write a markdown summary to:
`~/Vault/Claude-Code/01-analysis/youtube/YYYY-MM-DD-yt-digest.md`

Include date, video count, playlist breakdown, and bullet summaries for each video.

## Output

Confirm Slack posted + Vault path.
