---
description: Format AI provider credit/billing summary from API checks
argument-hint: (no arguments — reads check node outputs)
---

# API Credits — Report

**Workflow ID**: $WORKFLOW_ID

---

## Parse provider data

Read the four check node outputs:
- `$check-anthropic.output` — Anthropic usage API response
- `$check-openai.output` — OpenAI credit grants response
- `$check-openrouter.output` — OpenRouter key info
- `$check-google.output` — Google Cloud project/billing
- `$check-misc.output` — Kimi, Groq, Mistral, etc.

---

## Format the credits report

Use this layout:

```
💳 AI Provider Credits — YYYY-MM-DD

┌────────────────────┬──────────────┬─────────────┬──────────────────┐
│ Provider           │ Balance/Tier │ Used (mo)   │ Status           │
├────────────────────┼──────────────┼─────────────┼──────────────────┤
│ Anthropic          │ $X.XX        │ $X.XX       │ ✅ OK            │
│ OpenAI             │ $X.XX        │ $X.XX       │ ✅ OK            │
│ OpenRouter         │ $X.XX        │ $X.XX       │ ✅ OK            │
│ Google (paid proj) │ Paid tier    │ —           │ ✅ Billing on    │
│ Google (free proj) │ Free tier    │ —           │ ℹ️  Free limits  │
│ Groq               │ Free tier    │ —           │ ✅ Free          │
│ Kimi / Moonshot    │ Key present  │ —           │ ❓ No balance API│
└────────────────────┴──────────────┴─────────────┴──────────────────┘

⚠️ Low balance alerts (< $10):
  [list any providers running low]

ℹ️ No balance API available for: [list any where we couldn't check]

💡 Free tier recommendation:
  For Hermes/non-critical usage, prefer [provider] — free tier with
  [rate limits].

📁 Keys found in ~/.env:
  Anthropic ✓ | OpenAI ✓ | OpenRouter ✓ | Google (N keys) | Groq ✓ | ...
```

**Confidence notes:**
- If an API call failed or returned an error, note it clearly
- If a provider doesn't expose a balance API, show "Key present, no balance endpoint"
- For Google: check whether projects have billing enabled (Open = billing on, not just whether there's a key)

---

## Save to Vault

```bash
REPORT_DATE=$(date '+%Y-%m-%d')
REPORT_DIR=~/Vault/Claude-Code/01-analysis/api-credits
mkdir -p "$REPORT_DIR"
```

Save to: `~/Vault/Claude-Code/01-analysis/api-credits/YYYY-MM-DD-api-credits.md`

---

## Output

Display the full formatted table inline. Confirm Vault save path.
Flag any providers with balances under $10 or that look like they might run out soon.
