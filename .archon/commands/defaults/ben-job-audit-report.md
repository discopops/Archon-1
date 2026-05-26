---
description: Generate the final Hermes job health audit report, save to Vault, and deliver summary to Slack
argument-hint: (no arguments — reads all artifact files from upstream nodes)
---

# Hermes Job Audit — Report

**Workflow ID**: $WORKFLOW_ID

---

## Your Mission

Synthesise all upstream findings into a human-readable report. Save a full markdown report to the Vault. Deliver a Slack-friendly summary to `#all-discopops`. Persist state so next week's run can diff against this one.

---

## Phase 1: LOAD

```bash
cat $ARTIFACTS_DIR/jobs-manifest.json
cat $ARTIFACTS_DIR/audit-findings.json
cat $ARTIFACTS_DIR/audit-fixes.json 2>/dev/null || echo "{\"fixes\":[]}"
```

Also check for a prior state file to enable week-on-week delta reporting:

```bash
cat ~/.archon/ben-hermes-audit-state.json 2>/dev/null || echo "{}"
```

---

## Phase 2: ANALYSE — Delta from Prior Run

If prior state exists, compare:
- **Newly broken since last run** — jobs that were Working/Degraded before and are now Broken
- **Recovered since last run** — jobs that were Broken/Degraded and are now Working
- **Persistent failures** — jobs that were Broken last run AND still Broken (note how many weeks)

---

## Phase 3: GENERATE — Vault Report

Write the full markdown report to:
```
~/Vault/Claude-Code/03-reference/automation-health/hermes-job-audit-YYYY-MM-DD.md
```

Create `~/Vault/Claude-Code/03-reference/automation-health/` if it doesn't exist.

Report format:

```markdown
# Hermes Job Health Audit — YYYY-MM-DD

**Run by:** Archon workflow `ben-hermes-job-audit`
**Total active jobs:** N
**Overall health:** N% healthy (N working / N degraded / N broken)

---

## Summary

| Status | Count | Jobs |
|--------|-------|------|
| ✅ Working | N | Job A, Job B, ... |
| ⚠️ Degraded | N | Job C, ... |
| ❌ Broken | N | Job D, Job E, ... |
| ⏳ No data | N | Job F, ... |

---

## Changes Since Last Audit
*(Omit if first run)*

- 🔴 **Newly broken:** Job X (was Working on YYYY-MM-DD)
- 🟢 **Recovered:** Job Y (was Broken since YYYY-MM-DD)
- 🔴 **Still broken:** Job Z (broken for N consecutive audits)

---

## P1 — Fix Today

*(Jobs that are Broken AND have a daily deliver target)*

### ❌ [Job Name]
- **Schedule:** 7:30am daily
- **Delivers to:** Slack #all-discopops
- **Root cause:** [diagnosis]
- **Fix:** [specific action]
- **Applied automatically:** yes/no

---

## P2 — Fix This Week

*(Broken jobs with no delivery + degraded daily jobs)*

### ⚠️ [Job Name]
- **Schedule:** ...
- **Issue:** [description]
- **Suggested fix:** [action]

---

## P3 — Low Priority

*(Degraded weekly jobs, minor quality issues)*

---

## Working Jobs (No Action Needed)

| Job | Schedule | Last Run | Delivery |
|-----|----------|----------|---------|
| [Name] | 7am daily | YYYY-MM-DD | Slack |

---

## Manual Actions Required

*(Things that cannot be automated — need human attention)*

1. **Re-authenticate Composio Gmail** — Daily Jobs Alert and Gmail Morning Triage both depend on this. Open an interactive session and run `composio add gmail`.
2. [Other manual steps...]

---

## Automated Fixes Applied This Run

| Job | Fix Applied |
|-----|-------------|
| YouTube Daily Digest | `chmod +x ~/Scripts_&_Tools/YouTube_Automation/run_digest.sh` |

---

*Next audit due: approximately YYYY-MM-DD*
*State saved to: `~/.archon/ben-hermes-audit-state.json`*
```

---

## Phase 4: PERSIST STATE

Write `~/.archon/ben-hermes-audit-state.json`:

```json
{
  "last_audit_date": "YYYY-MM-DD",
  "job_statuses": {
    "<job-id>": {
      "status": "working|degraded|broken|no_data",
      "consecutive_failures": N,
      "first_seen_broken": "YYYY-MM-DD or null"
    }
  }
}
```

---

## Phase 5: DELIVER — Slack Summary

Post a Slack message to `#all-discopops` via the Composio Slack tool. Use bullet-list format only — NO markdown tables.

**Message format:**

```
*Hermes Job Audit — YYYY-MM-DD*
N active jobs · N working · N degraded · N broken

*P1 — Fix today:*
• ❌ Daily Jobs Alert — Composio auth expired
• ❌ YouTube Daily Digest — script permission fixed ✅ (auto-applied)

*P2 — Fix this week:*
• ⚠️ Wiki Daily Sync — MLX_URL env var missing

*Manual action needed:*
• Re-auth Composio Gmail (affects 3 jobs)

Full report: ~/Vault/Claude-Code/03-reference/automation-health/hermes-job-audit-YYYY-MM-DD.md
```

If Composio Slack is unavailable (likely in a cron context), skip delivery silently and note it in the report.

---

## Phase 6: IMESSAGE ALERT (P1 only)

If any P1 jobs are still broken AFTER automated fixes were applied, send an iMessage to `benowilliams@gmail.com`:

```
Hermes audit YYYY-MM-DD: N P1 jobs still broken after auto-fix.
Needs manual: [comma-separated job names]
Full report: ~/Vault/automation-health/hermes-job-audit-YYYY-MM-DD.md
```

Use the `send_imessage` tool. Only fire if P1 jobs remain broken — do not send for P2/P3 only.

---

## Phase 7: FINAL OUTPUT

Print a one-line completion summary:

```
Audit complete: N working, N degraded, N broken. N auto-fixed. Report: ~/Vault/.../hermes-job-audit-YYYY-MM-DD.md
```
