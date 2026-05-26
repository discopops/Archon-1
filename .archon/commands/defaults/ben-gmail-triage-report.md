---
description: Post Gmail triage summary to Slack #all-discopops
argument-hint: (no arguments — reads triage-findings.json)
---

# Gmail Triage — Report

**Workflow ID**: $WORKFLOW_ID

---

## Load

```bash
cat $ARTIFACTS_DIR/triage-findings.json
```

## Format Slack message

Slack plain text only — NO markdown tables.

```
*Gmail Morning Triage — DD Mon YYYY*
N emails processed · N require action · N archived

*🔴 Urgent (action today):*
• From: sender — Subject (one line)
• ...

*🟡 Important (action this week):*
• From: sender — Subject
• ...

(Omit sections with zero items)
```

## Deliver

Post to Slack `#all-discopops` via Composio `SLACK_SEND_MESSAGE`.

If Slack unavailable: print the summary to stdout only. Do not error.
