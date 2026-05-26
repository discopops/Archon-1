---
description: Fetch job-alert emails from Gmail, label as processed, and save raw content
argument-hint: (no arguments)
---

# Job Alerts — Fetch Emails

**Workflow ID**: $WORKFLOW_ID

---

## Fetch job alert emails

Use Composio `GMAIL_FETCH_EMAILS` (or `GMAIL_SEARCH_MESSAGES`) with this query:

```
subject:(job alert OR new jobs OR jobs matching OR job opportunities OR jobs for you) newer_than:2d
```

Also run a second pass for direct digest emails:

```
from:(seek.com.au OR linkedin.com OR indeed.com OR jora.com OR adzuna.com.au OR ethicaljobs.com.au) newer_than:2d
```

For each email returned, capture:
- `id` (message ID)
- `subject`
- `from`
- `date`
- `snippet`
- Full body text (plain text preferred, HTML fallback — strip tags)

## Label and archive processed messages

For all fetched message IDs, use `GMAIL_BATCH_MODIFY_MESSAGES` (or individual `GMAIL_MODIFY_MESSAGE`):
- Add label: `Label_57` (the "Jobs" processed label)
- Remove labels: `UNREAD`, `INBOX`

This ensures idempotency — re-runs won't double-process the same emails.

## Save output

Write to `$ARTIFACTS_DIR/raw_emails.json`:

```json
{
  "fetched_at": "ISO-8601 timestamp",
  "email_count": N,
  "emails": [
    {
      "id": "...",
      "subject": "...",
      "from": "...",
      "date": "...",
      "body": "full plain-text body"
    }
  ]
}
```

## Output

Report:
- Number of emails fetched
- Number labelled/archived
- Any fetch errors or empty results
