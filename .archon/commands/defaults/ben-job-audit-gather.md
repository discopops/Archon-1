---
description: Gather all active Hermes cron jobs and their latest output for the health audit
argument-hint: (no arguments — reads ~/.hermes/cron/ directly)
---

# Hermes Job Audit — Gather

**Workflow ID**: $WORKFLOW_ID

---

## Your Mission

Read the Hermes cron job registry and the latest output for every active job. Produce a structured JSON manifest that the downstream classify node will analyse.

---

## Phase 1: LOAD — Read the Job Registry

```bash
cat ~/.hermes/cron/jobs.json
```

Extract every job where `"enabled": true`. For each job, capture:
- `id`
- `name`
- `schedule` (cron expression)
- `prompt` (first 400 chars is sufficient for classify context)
- `deliver` (slack / imessage / none)
- `model` (if present)
- `skills` (if present)

---

## Phase 2: READ — Latest Output per Job

For each enabled job, find and read its latest run output:

```bash
JOB_ID="<id>"
OUTPUT_DIR="$HOME/.hermes/cron/output/$JOB_ID"

# Find the most recent output file (timestamped .md files)
LATEST=$(ls -t "$OUTPUT_DIR"/*.md 2>/dev/null | head -1)

if [ -n "$LATEST" ]; then
  echo "=== FILE: $LATEST ==="
  cat "$LATEST"
else
  echo "=== NO OUTPUT FOUND ==="
fi
```

Repeat for every enabled job. Run these reads in parallel where possible.

---

## Phase 3: BUILD — Manifest

Write a JSON manifest to `$ARTIFACTS_DIR/jobs-manifest.json` with this structure:

```json
{
  "audit_date": "YYYY-MM-DD",
  "total_active": N,
  "jobs": [
    {
      "id": "job-id",
      "name": "Human Job Name",
      "schedule": "30 7 * * *",
      "schedule_human": "7:30am daily",
      "deliver": "slack",
      "prompt_excerpt": "first 400 chars of prompt...",
      "last_output_file": "~/.hermes/cron/output/<id>/2026-05-17T07-30.md",
      "last_output_date": "YYYY-MM-DDTHH:MM",
      "last_output_excerpt": "first 800 chars of output...",
      "has_output": true
    }
  ]
}
```

For jobs with no output at all, set `"has_output": false` and `"last_output_excerpt": null`.

---

## Phase 4: OUTPUT

Print a brief summary to stdout:
```
Gathered N active jobs. N have recent output, N have no output yet.
Manifest written to $ARTIFACTS_DIR/jobs-manifest.json
```

Then emit the full manifest JSON as your final output so downstream nodes can consume it directly.
