---
description: Deduplicate, rank, and format the AI developments digest — then save to Vault and sync wiki
argument-hint: (no arguments — reads scan node outputs)
---

# AI Developments — Format & Save

**Workflow ID**: $WORKFLOW_ID

---

## Load raw findings

```bash
# Check most recent saved digest to avoid repeats
ls -t ~/Vault/Claude-Code/03-reference/ai-developments/daily-ai-agent-llm-digest-*.md 2>/dev/null | head -1 | xargs cat 2>/dev/null | head -60
```

You have two JSON arrays from the scan nodes:
- `$scan-labs.output` — findings from official AI lab sources
- `$scan-frameworks.output` — findings from agent framework and protocol sources

---

## Deduplicate and rank

1. Merge both arrays into one list
2. Remove any item already in the most recent saved digest
3. Remove items older than 24 hours
4. Rank remaining items by impact for agent builders using these criteria (highest to lowest):
   - New managed agent platform or major SDK release
   - New protocol (MCP, A2A) or interoperability standard
   - Significant new model affecting agent architecture
   - New agent runtime or harness feature
   - Framework changelog with production-grade additions (safety, memory, observability)
   - Minor UX updates, pricing changes, docs-only edits → exclude

Keep 3–5 items maximum. If no qualifying items, note this clearly and stop after saving.

---

## Format the digest

Use this exact structure:

```markdown
# Daily AI Agent & LLM Digest — DD-MM-YYYY

[If no items: "No qualifying AI agent or LLM developments in the last 24 hours."]

---

### 1. [Title] — [Lab/Org]

Published: DD-MM-YYYY
Source: [full URL]

**Summary**: [1–2 sentences on what shipped]

**Impact for builders**: [why it matters — reduces infra work / adds A2A / locks into closed harness / etc.]

**Tags**: [Lab] | [Category: LLM / Agent Framework / SDK / Managed Platform / Protocol]

---

[Continue for all ranked items, numbered 2, 3, 4, 5]
```

---

## Save to Vault

```bash
DIGEST_DATE=$(date '+%Y-%m-%d')
DIGEST_DIR=~/Vault/Claude-Code/03-reference/ai-developments
mkdir -p "$DIGEST_DIR"
```

Save to: `~/Vault/Claude-Code/03-reference/ai-developments/daily-ai-agent-llm-digest-YYYY-MM-DD.md`

---

## Sync to personal wiki

```bash
bash "/Users/BLW_M2_HOME/Scripts_&_Tools/wiki_builder/run_ai_developments_wiki.sh" 2>&1
```

If the wiki sync script does not exist or fails, log the error but do not fail the workflow — the Vault save is the primary deliverable.

---

## Output

Display the full digest inline. Confirm the Vault path and wiki sync status (success / skipped / failed with reason).

**Note**: All dates shown in the digest content must use `DD-MM-YYYY` format. The filename stays `YYYY-MM-DD` for filesystem sorting.
