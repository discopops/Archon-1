---
description: Build cross-agent skills matrix and coverage report from scan outputs
argument-hint: (no arguments — reads scan node outputs)
---

# Skills Matrix — Report

**Workflow ID**: $WORKFLOW_ID

---

## Parse scan outputs

- `$scan-canonical.output` — canonical store at `~/.agents/skills/`
- `$scan-claude-code.output` — Claude Code `~/.claude/skills/` + commands
- `$scan-codex.output` — Codex `~/.codex-home/skills/`
- `$scan-gemini.output` — Antigravity `~/.gemini/` + Hermes `~/.hermes/skills/`

---

## Build the matrix

For each skill found in any location, create a row:

```
Skill Name                  │ Canonical │ Claude Code │ Codex │ Antigravity │ Hermes
─────────────────────────────┼───────────┼─────────────┼───────┼─────────────┼────────
agent-frameworks             │    ✓      │      ✓      │   ✓   │     ✓       │   ✓
chart-analysis               │    ✓      │      ✓      │   ✓   │      —      │   —
crypto-trading-workflow      │    ✓      │      ✓      │   ✓   │      —      │   ✓
...
```

Use `✓` for present, `—` for absent.

**Sort order**: skills present in most agents first, then alphabetically within each group.

---

## Summary section

```
📊 Skills Matrix — YYYY-MM-DD

TOTALS
  Canonical store:  NNN skills
  Claude Code:      NNN skills
  Codex:            NNN skills
  Antigravity:      NNN skills
  Hermes:           NNN skills

COVERAGE
  Everywhere (all 5):   N skills
  Missing in ≥1 agent:  N skills
  Claude Code only:     N skills  (not synced to others)
  Codex only:           N skills

GAP HIGHLIGHTS (skills missing somewhere they should be)
  • [skill name] present in Claude Code but absent in Codex
  • [skill name] in canonical but not synced to Antigravity
  [show top 5 gaps]

SKILLS WITH MOST COVERAGE GAPS
  1. [skill] — missing from: [agents]
  2. [skill] — missing from: [agents]
  ...

💡 To resync: run python3 ~/Scripts_&_Tools/Sync_Scripts/relink_skills.py
```

---

## Save to Vault

```bash
REPORT_DATE=$(date '+%Y-%m-%d')
mkdir -p ~/Vault/Claude-Code/03-reference/skills/
```

Save the full matrix table + summary to:
`~/Vault/Claude-Code/03-reference/skills/YYYY-MM-DD-skills-matrix.md`

---

## Output

Display the summary + top gap highlights inline (not the full table — that goes to Vault).
Confirm Vault save path and total skills count per agent.
