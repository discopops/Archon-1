---
description: Filter, score 0-100, and tier job listings against Ben's profile
argument-hint: (no arguments — reads jobs_extracted.json)
---

# Job Alerts — Score and Tier

**Workflow ID**: $WORKFLOW_ID

---

## Load

```bash
cat $ARTIFACTS_DIR/jobs_extracted.json
```

---

## Step 1 — Location filter

Keep jobs where location meets at least one criterion:
- Contains: Brisbane, QLD, Queensland, Gold Coast, Sunshine Coast, Ipswich
- OR location_type = remote
- OR contains: "Australia" + "remote" / "work from anywhere" / "flexible location"

Exclude: Sydney-only, Melbourne-only, Perth-only, overseas roles without remote option.

## Step 2 — Relevance filter

**Keep** roles related to: risk, governance, compliance, audit, assurance, regulatory, policy, legal, privacy, ESG, sustainability, change management, strategy, operations (senior).

**Exclude** roles primarily in: construction/site management, automotive, medical officer/clinician, IT engineering (dev/infra with no risk/gov context), sales, marketing, hospitality, administration/reception, finance analyst (no risk component).

## Step 3 — Score each role (0–100)

Apply these weights:

| Dimension | Max | Criteria |
|---|---|---|
| Seniority | 40 | Head/GM/EGM=40, Director=35, Principal/Senior Manager=30, Manager=25, Senior=20, Mid=15, Junior/Grad=5 |
| Sector | 25 | Financial services/Super/Investments=25, Gov/RegBodies=22, Professional services=18, NFP/Health=16, Other=10 |
| Function | 25 | Risk+Gov+Compliance+Audit (all 3+)=25, Any 2=20, Risk only=18, Governance only=15, Adjacent=10 |
| AI/Tech angle | 10 | AI-enabled risk / cyber / data / digital risk = 10, Some tech = 5, None = 0 |
| Salary | 15 | $150k+=15, $130–149k=12, $110–129k=9, $90–109k=6, <$90k=3, Not stated=7 |

Cap total at 100.

## Step 4 — Tier

- **Tier 1**: score ≥ 65 — best alignment, prioritise applying
- **Tier 2**: score 45–64 — strong alignment, worth reviewing
- **Tier 3**: score < 45 — partial match, lower priority

## Save output

Write to `$ARTIFACTS_DIR/scored_jobs.json`:

```json
{
  "scored_at": "ISO-8601 timestamp",
  "total_after_location_filter": N,
  "total_after_relevance_filter": N,
  "tier1_count": N,
  "tier2_count": N,
  "tier3_count": N,
  "jobs": [
    {
      ...all extracted fields...,
      "score": 78,
      "tier": 1,
      "score_breakdown": { "seniority": 30, "sector": 22, "function": 20, "ai_tech": 5, "salary": 12 },
      "why_it_fits": "One sentence referencing Ben's background"
    }
  ]
}
```

## Output

Report tier counts and top 3 Tier 1 titles.
