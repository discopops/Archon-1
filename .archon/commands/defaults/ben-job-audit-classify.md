---
description: Classify each Hermes cron job as Working / Degraded / Broken based on latest output vs prompt objective
argument-hint: (no arguments — reads jobs-manifest.json from artifacts)
---

# Hermes Job Audit — Classify

**Workflow ID**: $WORKFLOW_ID

---

## Your Mission

Read the job manifest and latest outputs. For every job, compare what the output actually delivered against what the prompt asked for. Classify each job and write a findings file.

---

## Phase 1: LOAD

```bash
cat $ARTIFACTS_DIR/jobs-manifest.json
```

---

## Phase 2: ANALYSE — Classify Each Job

For each job in the manifest, apply this decision logic:

### Classification Rules

| Status | Criteria |
|--------|----------|
| ✅ **Working** | Output exists AND the core deliverable matches the prompt objective (right format, right content, no error messages as the primary output) |
| ⚠️ **Degraded** | Output exists but is partial, lower quality than expected, missing key sections, or shows warnings that suggest reduced functionality |
| ❌ **Broken** | No output, OR output is entirely an error message / stack trace, OR output explicitly says a step failed with no fallback result |
| ⏳ **No data** | Job has never run (no output file at all) — cannot classify yet |

### What to look for in outputs

**Signs of Broken:**
- `Error:`, `Exception:`, `Traceback`, `FAILED`, `command not found`, `Connection refused`
- Composio MCP unreachable / session not established
- Permission denied on script files
- CLI syntax errors (`unknown option`, `command not found`)
- Output is just an error and nothing else was produced

**Signs of Degraded:**
- Report delivered but key sections are empty or say "N/A" / "no data"
- Partial results (e.g. "3 of 11 jobs processed")
- Warnings present but some output still produced
- Output format wrong for delivery channel (e.g. markdown tables in Slack)
- Correct shape but clearly wrong/stale data (e.g. yesterday's date, zero results when some expected)

**Signs of Working:**
- The prompt's stated objective is clearly met
- Delivery channel received the right content
- Numbers/dates are current and plausible
- No error messages dominating the output

### For jobs with no output file

Mark as ⏳ No data. Do NOT mark as Broken — they may be scheduled for a future time or were just created.

---

## Phase 3: BUILD — Findings File

Write `$ARTIFACTS_DIR/audit-findings.json`:

```json
{
  "audit_date": "YYYY-MM-DD",
  "summary": {
    "total": N,
    "working": N,
    "degraded": N,
    "broken": N,
    "no_data": N
  },
  "jobs": [
    {
      "id": "job-id",
      "name": "Job Name",
      "status": "working|degraded|broken|no_data",
      "status_emoji": "✅|⚠️|❌|⏳",
      "one_line_reason": "Concise diagnosis — what specifically is wrong or right",
      "root_cause_hint": "Composio MCP unreachable|CLI syntax changed|Permission denied|Script path wrong|null",
      "last_run": "YYYY-MM-DDTHH:MM or null",
      "priority": "p1|p2|p3|skip"
    }
  ]
}
```

**Priority rules:**
- `p1` — Broken jobs that run daily and have an iMessage or Slack deliver target (high visibility)
- `p2` — Broken jobs with no delivery, OR Degraded daily jobs
- `p3` — Degraded weekly jobs, minor quality issues
- `skip` — Working jobs, No-data jobs (not yet run)

---

## Phase 4: OUTPUT

Print a classification table to stdout:

```
## Hermes Job Audit — Classification

| Job | Status | Reason |
|-----|--------|--------|
| Daily Jobs Alert | ❌ Broken | Composio MCP unreachable |
| YouTube Daily Digest | ❌ Broken | Permission denied on shell script |
| Gmail Morning Triage | ✅ Working | 66 emails processed, 4 action items |
| ...

Summary: N working, N degraded, N broken, N no-data
```

Then emit the full findings JSON as your final output.
