---
description: Batch archive low-priority Gmail messages and mark notifications as read
argument-hint: (no arguments — reads triage-findings.json)
---

# Gmail Triage — Archive

**Workflow ID**: $WORKFLOW_ID

---

## Load

```bash
cat $ARTIFACTS_DIR/triage-findings.json
```

## Archive low-priority

Use Composio `GMAIL_BATCH_MODIFY_MESSAGES` on all IDs in `low_priority`:
- `remove_labels: ["INBOX", "UNREAD"]`
- Process in batches of 100 if needed

## Mark notifications as read

Use `GMAIL_BATCH_MODIFY_MESSAGES` on all IDs in `notification`:
- `remove_labels: ["UNREAD"]`
- Keep in INBOX (they may need a glance)

## Leave untouched

`actionable_urgent` and `actionable_important` stay in INBOX as-is.

## Output

Report counts: "Archived N low-priority. Marked N notifications as read. N actionable items remain in inbox."
