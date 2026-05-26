---
description: Format the intelligence brief and save to Vault
argument-hint: (no arguments — reads synthesis.json)
---

# Intelligence — Brief

**Workflow ID**: $WORKFLOW_ID

---

## Load

```bash
cat $ARTIFACTS_DIR/synthesis.json
cat $ARTIFACTS_DIR/sources.json
```

---

## Format the intelligence brief

Write a structured intelligence brief using this template:

```markdown
# Intelligence Brief: [TOPIC]
**Date**: [YYYY-MM-DD] | **Classification**: Personal | **Confidence**: [overall HIGH/MEDIUM/LOW]

---

## Bottom Line Up Front

[2–3 sentences: what is happening, why it matters to Ben, what action (if any) is recommended]

---

## Key Findings

### 1. [Finding title]
[2–4 sentences elaborating the finding]
**Confidence**: HIGH | **Sources**: [source names/URLs]

### 2. [Finding title]
...

[Continue for all key findings]

---

## Background Context

[3–5 paragraphs: how did we get here, who are the key players, what are the competing narratives]

---

## What We Don't Know

- [Gap 1]
- [Gap 2]
- [Gap 3]

---

## Sources

[Numbered list: Title | URL | Date | Reliability notes]

---

## Recommended Actions

[If applicable: specific next steps for Ben. If purely informational: "No immediate action required — monitor [X]."]
```

---

## Save to Vault

```bash
TOPIC_SLUG=$(echo "$ARGUMENTS" | tr '[:upper:]' '[:lower:]' | tr ' ' '-' | tr -cd '[:alnum:]-' | cut -c1-50)
BRIEF_DATE=$(date '+%Y-%m-%d')
BRIEF_DIR=~/Vault/Claude-Code/01-analysis/intelligence
mkdir -p "$BRIEF_DIR"
```

Save to: `~/Vault/Claude-Code/01-analysis/intelligence/YYYY-MM-DD-[topic-slug].md`

## Output

Display the full brief inline, then confirm Vault save path.
