---
description: Fetch all unread Gmail inbox messages and classify each as Actionable / Low-Priority / Notification
argument-hint: (no arguments)
---

# Gmail Triage — Classify

**Workflow ID**: $WORKFLOW_ID

---

## Phase 1: Fetch

Use Composio `GMAIL_FETCH_EMAILS`:
- Query: `in:inbox is:unread`
- `max_results: 200`, `verbose: false`, `include_payload: false`

Capture total count and message IDs.

## Phase 2: Classify

For each message, classify using sender + subject alone (no body needed at this stage):

| Category | Criteria |
|----------|----------|
| **Actionable — Urgent** | Direct human reply needed, time-sensitive, meeting/deadline, payment, from a person not a service |
| **Actionable — Important** | Needs a response or action but not today |
| **Notification** | System alert, shipping, confirmation, receipt — read-only, no action required |
| **Low-Priority** | Newsletter, promotional, marketing, automated digest |

## Phase 3: Write findings

Write `$ARTIFACTS_DIR/triage-findings.json`:

```json
{
  "date": "YYYY-MM-DD",
  "total_unread": N,
  "actionable_urgent": [...message IDs + subject + sender],
  "actionable_important": [...],
  "notification": [...message IDs],
  "low_priority": [...message IDs]
}
```

Output a one-line summary: "N unread: X urgent, X important, X notifications, X low-priority."
