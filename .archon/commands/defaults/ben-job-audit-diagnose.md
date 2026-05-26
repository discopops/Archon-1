---
description: Deep-dive on Broken and Degraded Hermes jobs — diagnose root cause and write specific fix instructions
argument-hint: (no arguments — reads audit-findings.json from artifacts)
---

# Hermes Job Audit — Diagnose

**Workflow ID**: $WORKFLOW_ID

---

## Your Mission

For every job classified as Broken or Degraded, perform a targeted diagnosis and write a concrete, executable fix. Skip Working and No-data jobs entirely.

---

## Phase 1: LOAD

```bash
cat $ARTIFACTS_DIR/audit-findings.json
cat $ARTIFACTS_DIR/jobs-manifest.json
```

Filter to jobs with `status: "broken"` or `status: "degraded"`. If there are none, write an empty fixes file and exit cleanly.

---

## Phase 2: DIAGNOSE — Per-Job Deep Dive

For each broken/degraded job, investigate with these tools in priority order:

### 2a. Read the full latest output

```bash
cat ~/.hermes/cron/output/<job-id>/<latest-file>.md
```

Look for the exact error message, which step failed, and whether any fallback ran.

### 2b. Check the job prompt for clues

From the manifest `prompt_excerpt` — does the prompt reference:
- A script path? Check if it exists and is executable: `ls -la <path>`
- A CLI tool (`ft`, `composio`, `rtk`, `imsg`)? Check if installed: `which <tool>`
- An MCP or Composio connection? Note it — these can't be verified from shell alone
- An env var? Check it's set: `grep -i VAR_NAME ~/.env ~/.hermes/.env 2>/dev/null`

### 2c. Check related scripts and permissions

If the job calls a wrapper script in `~/.hermes/cron/scripts/` or `~/Scripts_&_Tools/`:
```bash
ls -la ~/.hermes/cron/scripts/<job-id>* 2>/dev/null
# Check executable bit
```

### 2d. Check for known root cause patterns

| Pattern | Check |
|---------|-------|
| `Composio MCP unreachable` | Note: requires interactive fix (re-auth). Cannot fix from here. |
| `Permission denied` | `chmod +x <script>` |
| `CLI not found` (`ft`, `rtk`, etc.) | `source ~/.nvm/nvm.sh && nvm use 22 && which <cli>` |
| `env var missing` | Check `~/.env` and `~/.hermes/.env` |
| `broken symlink` | `ls -la <path>` to confirm, then fix or note |
| `Playwright not installed` | `cd <project> && npx playwright install chromium` |
| Script path changed | Locate actual script: `find ~/Scripts_\&_Tools -name "<filename>" 2>/dev/null` |

---

## Phase 3: BUILD — Fix Instructions

Write `$ARTIFACTS_DIR/audit-fixes.json`:

```json
{
  "fixes": [
    {
      "id": "job-id",
      "name": "Job Name",
      "status": "broken|degraded",
      "priority": "p1|p2|p3",
      "root_cause": "One-sentence diagnosis of what is actually wrong",
      "fix_type": "automated|manual|needs_reauth|prompt_update|investigate_further",
      "fix_commands": [
        "chmod +x /path/to/script.sh",
        "source ~/.nvm/nvm.sh && nvm use 22"
      ],
      "prompt_patch": "Suggested change to the job prompt, if needed (or null)",
      "manual_steps": "What Ben needs to do manually (re-auth Composio, etc.) — or null",
      "confidence": "high|medium|low",
      "notes": "Any caveats or follow-up required"
    }
  ]
}
```

**fix_type values:**
- `automated` — fix can be applied immediately with shell commands in this session
- `prompt_update` — the job prompt needs editing in jobs.json (can be done here)
- `manual` — requires a one-time human action (e.g. opening a browser)
- `needs_reauth` — an OAuth/Composio connection has expired
- `investigate_further` — not enough signal to diagnose confidently

---

## Phase 4: APPLY AUTOMATED FIXES

For every fix with `fix_type: "automated"`, apply it now:

```bash
# Run each command in fix_commands sequentially
# Log each result
```

After applying, re-check the fix actually worked where possible (e.g. `ls -la` to confirm permission change).

For `prompt_update` fixes, patch `~/.hermes/cron/jobs.json` directly:
```bash
# Use python3 to safely update the JSON
python3 - <<'PYEOF'
import json, sys

with open('/Users/BLW_M2_HOME/.hermes/cron/jobs.json') as f:
    jobs = json.load(f)

for job in jobs:
    if job['id'] == '<job-id>':
        job['prompt'] = """<updated prompt text>"""
        break

with open('/Users/BLW_M2_HOME/.hermes/cron/jobs.json', 'w') as f:
    json.dump(jobs, f, indent=2)
print("Updated job prompt for <job-id>")
PYEOF
```

---

## Phase 5: OUTPUT

Print a diagnosis and actions summary:

```
## Diagnoses & Fixes Applied

### ❌ Daily Jobs Alert — BROKEN (P1)
Root cause: Composio MCP unreachable — session token expired
Fix type: needs_reauth
→ Manual: Re-run `composio add gmail` in an interactive session
Confidence: high

### ❌ YouTube Daily Digest — BROKEN (P1)
Root cause: Permission denied on ~/Scripts_&_Tools/YouTube_Automation/run_digest.sh
Fix type: automated
→ Applied: chmod +x ~/Scripts_&_Tools/YouTube_Automation/run_digest.sh ✅
Confidence: high

---
Applied: N automated fixes
Needs manual action: N
Needs investigation: N
```

Then emit the full fixes JSON as your final output.
