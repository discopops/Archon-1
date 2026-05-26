---
description: Synthesise Mac system data into a health score and prioritised report
argument-hint: (no arguments — reads system scan node outputs)
---

# Mac Health — Report

**Workflow ID**: $WORKFLOW_ID

---

## Inputs

- `$resources.output` — memory, CPU load, disk usage
- `$processes.output` — top processes by CPU/memory, launchd list
- `$thermal-storage.output` — thermal log, battery, large files, crashes

---

## Synthesise health score

Assess each area (score out of 10, 10 = perfect):
- **CPU**: idle ≥60% = 10, 40-60% = 7, 20-40% = 5, <20% = 2
- **Memory**: free pages > 20% = 10, 10-20% = 7, <10% = 4 (check swap usage)
- **Disk**: >30% free = 10, 15-30% = 7, 5-15% = 4, <5% = 1
- **Thermal**: no throttling = 10, minor = 6, active throttling = 2
- **Processes**: no runaway processes = 10, 1-2 heavy = 7, 3+ heavy = 4
- **Crashes**: no recent = 10, 1-2 = 7, 3+ = 4

Overall score = average of above (0–100 scale).

---

## Format the report

```
🖥️ Mac Health Report — YYYY-MM-DD HH:MM

HEALTH SCORE: XX/100  [colour: ≥80 green, 60-79 yellow, <60 red]

EXECUTIVE SUMMARY
[2 sentences: overall condition and the single most important issue]

RESOURCE STATUS
  CPU:     XX% idle  [load averages: 1m/5m/15m]
  Memory:  XX GB free / XX GB used  [swap: XX MB]
  Disk /:  XX GB free (XX%)
  Uptime:  Xd Xh Xm

TOP RISKS (sorted by impact)
  1. [Most critical issue + specific recommendation]
  2. [Second issue + recommendation]
  3. [Third issue + recommendation]

RESOURCE BOTTLENECK
  Current limit: [CPU / RAM / Disk I/O / Storage / Network / None]

HEAVY PROCESSES (>5% CPU or >5% MEM)
  process_name   CPU%   MEM%   PID
  [list up to 8]

LARGE FILES (>1GB)
  [list up to 5, with paths truncated]

RECENT CRASHES
  [list crash reports if any, or "None in recent logs"]

QUICK WINS (safe to do now)
  • [action 1]
  • [action 2]

PREVENTATIVE
  • [future-focused recommendation]
```

---

## Save to Vault

```bash
REPORT_DATE=$(date '+%Y-%m-%d')
mkdir -p ~/Vault/MacOS/health-reports
```

Save to: `~/Vault/MacOS/health-reports/YYYY-MM-DD-mac-health.md`

---

## Output

Display the full report inline. Confirm Vault save path.
Keep recommendations **read-only** — do not suggest deleting files or killing processes without explicit user confirmation.
