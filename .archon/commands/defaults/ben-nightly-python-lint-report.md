---
description: Format the Python lint scan results and post a compact Slack report
argument-hint: (no arguments — reads scan node output)
---

# Nightly Python Lint — Report

**Workflow ID**: $WORKFLOW_ID

---

## Parse scan output

Read `$scan.output` and extract:
- Run date
- Total files found (changed in last 7 days)
- Repos with changed files (and counts)
- Files with flake8/mypy issues (grouped by repo)
- Summary totals: files with issues, clean files, total checked

---

## Format the report

**If no changed files found:**
```
🐍 Python Lint — Clean Run

No Python files changed in the last 7 days under ~/GitHub.
Nothing to lint.
```

**If issues found:**
```
🐍 Python Lint Report — YYYY-MM-DD

📊 Summary
Files checked: N  |  With issues: N  |  Clean: N

📁 Repos scanned:
  crewAI              45 files
  langchain           12 files
  ...

⚠️ Issues by repo:

── crewAI ──
  agents/core.py: E501 line too long (134 > 120)
  agents/core.py: mypy: Argument 1 to "run" has incompatible type
  [show up to 5 issues per file, up to 10 files per repo]

── langchain ──
  ...

[If more than 10 repos have issues, show top 10 by issue count]

📝 Run flake8 <file> or mypy <file> locally to fix.
```

**If all files clean:**
```
🐍 Python Lint — All Clear ✅

Checked N Python files across M repos (changed in last 7 days).
No flake8 or mypy issues found.
```

---

## Post to Slack

Use SLACK_SEND_MESSAGE to post to `#all-discopops`.

Keep the message compact — use plain text (not tables), truncate long file paths to the last 2 components. Cap the total message at 50 lines.

---

## Save report

```bash
REPORT_DATE=$(date '+%Y-%m-%d')
REPORT_DIR=~/Vault/Claude-Code/01-analysis/python-lint
mkdir -p "$REPORT_DIR"
```

Save to: `~/Vault/Claude-Code/01-analysis/python-lint/YYYY-MM-DD-python-lint.md`

---

## Output

Confirm the Slack post and Vault path. Display the full formatted report inline.
