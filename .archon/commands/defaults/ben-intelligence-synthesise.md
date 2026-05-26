---
description: Cross-reference multi-source research findings and extract key insights
argument-hint: (no arguments — reads sources.json and context.json)
---

# Intelligence — Synthesise

**Workflow ID**: $WORKFLOW_ID

---

## Load

```bash
cat $ARTIFACTS_DIR/sources.json
cat $ARTIFACTS_DIR/context.json
```

---

## Cross-reference and analyse

With all sources loaded:

1. **Verify claims**: Which key facts appear across multiple sources? Which are single-source only?
2. **Identify tensions**: Where do sources contradict each other or offer competing interpretations?
3. **Extract signals**: What are the strongest, most actionable insights?
4. **Confidence rating**: For each key finding, rate confidence: HIGH (multiple corroborating sources), MEDIUM (single credible source), LOW (rumour, speculation, or single non-authoritative source)
5. **Gaps**: What is NOT known? What questions remain unanswered?

## Structure synthesis

Save to `$ARTIFACTS_DIR/synthesis.json`:

```json
{
  "topic": "...",
  "key_findings": [
    {
      "finding": "one clear sentence",
      "confidence": "HIGH|MEDIUM|LOW",
      "sources": ["url1", "url2"],
      "implications": "why this matters"
    }
  ],
  "tensions": ["..."],
  "gaps": ["..."],
  "bottom_line": "2–3 sentence executive summary"
}
```

## Output

The bottom_line and top 3 key findings.
