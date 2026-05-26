---
description: Synthesise RTK weekly data into a log entry, update wrap list if needed, alert on signal
argument-hint: (no arguments — reads upstream node outputs)
---

# RTK Weekly Review — Synthesise

**Workflow ID**: $WORKFLOW_ID

---

## Inputs

**RTK gain data:**
```
$rtk-gain.output
```

**Session adoption:**
```
$rtk-session.output
```

**Discover candidates (last 7 days, all projects):**
```
$rtk-discover.output
```

**CC economics:**
```
$rtk-economics.output
```

**Prior log (last 2 entries):**
```
$read-log.output
```

**Current wrap list:**
```
$read-usage-guide.output
```

---

## Phase 1: Calculate deltas

From the prior log entries, extract last week's cumulative token count. Compute:
- This week's tokens saved = current cumulative - last week's cumulative
- Delta % vs last week

From `rtk-session.output`, extract adoption rate (% of sessions using RTK).

Check two consecutive-week signals:
- **Kill signal**: TWO weeks with net savings <5,000 tokens AND adoption ≥60%
- **Usage signal**: adoption rate below 50% for TWO consecutive weeks

(Single quiet weeks are normal — do NOT alert on one week alone.)

## Phase 2: Check for new wrap candidates

From `rtk-discover.output`, identify any commands with >5,000 tokens/week potential that are NOT already in the wrap list.

**Outlier check**: if a single command accounts for >80% of weekly savings, note "outlier-driven week" — these are not a sustainable baseline.

## Phase 3: Append log entry

Append to `~/Vault/Claude-Code/02-implementation/rtk-evaluation/WEEKLY_GAIN_LOG.md`:

```markdown
## YYYY-MM-DD (Week N)
- **Cumulative tokens saved:** X
- **This week:** Y (delta vs last week: ±Z%)
- **Adoption rate:** N% of sessions used rtk
- **Top 5 wrapped commands:** ...
- **Missed-wrap candidates:** ... (or "none")
- **Verdict:** keep | expand | shrink | kill
- **Notes:** one sentence of judgement
```

Keep the entry under 200 words. Preserve original `first_seen` dates for carry-over items.

## Phase 4: Update wrap list (if new candidate found)

If `rtk-discover` surfaced a command with >5k tokens/week potential not already wrapped:

Edit `~/Vault/Claude-Code/02-implementation/rtk-evaluation/USAGE_GUIDE.md` — add to the "Always wrap" table with a brief reason.

## Phase 5: Send iMessage (conditional)

Fire an iMessage to `benowilliams@gmail.com` ONLY if one of these signals fired:
- Kill signal (two consecutive weeks, <5k tokens, ≥60% adoption)
- Expand signal (new high-value wrap candidate found)
- Usage signal (adoption <50% for two consecutive weeks)

Message format:
```
RTK weekly: [signal] — [one-line summary]. Log: ~/Vault/Claude-Code/02-implementation/rtk-evaluation/WEEKLY_GAIN_LOG.md
```

If no signal fired, commit silently. **No final summary printout** — the log entry is the output.

## Constraints

- Do NOT modify CLAUDE.md, MEMORY.md, or anything outside `~/Vault/Claude-Code/02-implementation/rtk-evaluation/`
- Do NOT run `rtk init -g` or change RTK config
- If any rtk command failed: write the failure to the log and send a one-line iMessage flagging it
