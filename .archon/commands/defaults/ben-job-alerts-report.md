---
description: Post the daily job alerts Slack briefing and persist artefacts
argument-hint: (no arguments — reads scored_jobs.json)
---

# Job Alerts — Report

**Workflow ID**: $WORKFLOW_ID

---

## Load

```bash
cat $ARTIFACTS_DIR/scored_jobs.json
```

---

## Format the Slack briefing

Post to `#all-discopops` using Composio `SLACK_SEND_MESSAGE`.

Use plain text (NO markdown tables). Format:

```
Daily Job Alerts — [DD Month YYYY]

Emails processed: [N] | Qualifying roles: [N] | Excluded: [N]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Tier 1 — Best Alignment (≥65)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[For each Tier 1 job, sorted by score descending:]
N. [Title] | [Company]
   📍 [Location] | 💰 [Salary]
   🔗 [link or "No direct link"]
   Why it fits: [why_it_fits from scored_jobs.json]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Tier 2 — Strong Alignment (45–64)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Tier 2 jobs, condensed — title | company | location | salary | link]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Tier 3 — Worth a Look (<45)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Tier 3 jobs, condensed — top 5 max]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 Top picks to action first: [top 2 Tier 1 titles]
```

If no qualifying roles found: `No qualifying Brisbane/remote risk-governance-compliance roles today. [N] emails processed, [N] total jobs filtered.`

## Persist artefacts

Copy scored_jobs.json to Vault:
```bash
cp $ARTIFACTS_DIR/scored_jobs.json \
  ~/Vault/Claude-Code/01-analysis/jobs/$(date '+%Y-%m-%d')-job-alerts.json
```

## Output

Confirm Slack posted + Vault save path.
