---
description: Compare installed plugin versions against npm registry and report updates
argument-hint: (no arguments — reads list-plugins node output)
---

# Plugin Update — Report

**Workflow ID**: $WORKFLOW_ID

---

## Load installed plugins

The previous node (`list-plugins`) captured `claude plugin list` output. Parse:
- Plugin name (e.g. `codex@openai-codex`, `imessage@claude-plugins-official`)
- Installed version
- Scope: user or project

If `PLUGIN_CMD_UNAVAILABLE` is in the output, report: "Claude plugin CLI unavailable — ensure Claude Code is up to date" and stop.

---

## Check latest versions

For each installed plugin, check npm for the latest version:

```bash
# For each plugin package name:
npm view <package-name> version 2>/dev/null
# e.g.: npm view @anthropic-ai/claude-plugins-official version
```

Run these in parallel if possible. Package names follow the pattern:
- `@anthropic-ai/claude-plugins-official` for official plugins
- `@openai/codex` for codex
- Use the scope field from `claude plugin list` to infer the npm package name

---

## Format report

**If all up to date:**
```
✅ All plugins are current.

Installed (N total):
• plugin-name — v1.2.3 ✅
```

**If updates available:**
```
🔄 Plugin Updates Available

Outdated (N):
┌─────────────────────────────────┬───────────┬────────┐
│ Plugin                          │ Installed │ Latest │
├─────────────────────────────────┼───────────┼────────┤
│ codex@openai-codex              │ 1.0.2     │ 1.0.4  │
│ imessage@claude-plugins-official│ 0.0.1     │ 0.1.0  │
└─────────────────────────────────┴───────────┴────────┘

Current (N):
• plugin-name — v1.2.3 ✅

To update all, run:
  claude plugin update --all
Or update individually:
  claude plugin update codex
  claude plugin update imessage
```

**Note:** After updating, restart Claude Code for changes to take effect.

---

## Update (if run with --update flag or user confirms)

If `$ARGUMENTS` contains "update" or "yes":

```bash
claude plugin update --all 2>&1
```

Report results: which updated successfully, which failed.

---

## Output

The formatted report above. If updates were applied, confirm with a before/after summary.
