---
description: Format the full daily crypto report, send Telegram summary, save to Vault
argument-hint: (no arguments — reads /tmp/cs_portfolio.json and tv_analysis.json)
---

# Crypto Daily Report — Format and Deliver

**Workflow ID**: $WORKFLOW_ID

---

## Load

```bash
cat /tmp/cs_portfolio.json
cat $ARTIFACTS_DIR/tv_analysis.json
cat "/Users/BLW_M2_HOME/Scripts_&_Tools/crypto/daily_trade_plan.json" | python3 -m json.tool 2>/dev/null
tail -5 "/Users/BLW_M2_HOME/Scripts_&_Tools/logs/trade_executor.log" 2>/dev/null
```

---

## Format the full report

Use this exact template:

```
═══════════════════════════════════════════════════════════
  DAILY CRYPTO REPORT — [DD MMM YYYY]  [HH:MM UTC]
═══════════════════════════════════════════════════════════

## AUTOMATED SYSTEM STATUS
- Executor: [✅ Running / ⚠️ Check required] — com.user.crypto-trade-executor
- Active conditions: [N] | Triggered since last report: [N]
- ⚠️ Near-trigger alerts: [conditions within 5 RSI pts or 5% price of threshold, or "None"]
- Plan expires: [expires_at from trade plan JSON]
- Last executor log: [last relevant line]

## PORTFOLIO OVERVIEW
- Total: AUD $[total_aud] | Cash: AUD $[aud_cash] ([cash_pct]%)
- Cash target: 15% — [✅ Above target / ⚠️ Below target]
- Holdings: [N coins]

## CURRENT HOLDINGS

[For each coin with aud_value ≥ AUD $5, sorted by aud_value descending:]

### [SIGNAL_EMOJI] [COIN] — [SIGNAL LABEL]
- Balance: [qty] | AUD: $[aud_value] ([portfolio_pct]%)
- Rate: AUD $[current_rate] | Avg buy: AUD $[avg_buy_rate] | P&L: [pct_change]% / AUD $[abs_gain_aud]
- RSI (Binance): [rsi] · 24h: [24h_pct]% · TV: [bullish/bearish/neutral/unavailable]
- EMA context: [short EMA alignment] / 4H bias: [positive/negative]
- Active conditions: [list relevant conditions from trade plan, or "None"]
- Rationale: [one sentence from tv_analysis]

## NEW OPPORTUNITIES
[Short-term flags:]
Short-term (1–4 weeks):
[list flagged coins with RSI, drawdown, executor status]

Long-term (3–12 months):
[list flagged coins with RSI, price vs 100d high, executor status]

## ACTION ITEMS
[Priority table:]
Auto-handled by executor: [list]
Manual review needed: [list with reason]
Watch only (0 cash): [list if cash < 5%]
```

**Critical notes:**
- ALL CoinSpot prices are AUD — never compare directly to USDT
- If cash = 0%: all opportunities are WATCH ONLY
- Illiquid coins (GBYTE, PPC, NAV, MTH, etc.) — do NOT recommend trading
- QNT stop-loss: if rate < AUD $111, flag immediately
- If TV unavailable for a coin: note "TV unavailable — RSI/P&L only"

---

## Send Telegram summary

```bash
python3 ~/Scripts/telemsg "
📊 DAILY CRYPTO — $(date '+%d %b %Y')
💼 AUD $[TOTAL] | $[CASH] cash ([CASH_PCT]%) [⚠️ BELOW TARGET / ✅ OK]
🤖 Executor: [N] conditions active[, [N] triggered]
📈 Best: [TOP_COIN] [P&L%]  📉 Worst: [BOTTOM_COIN] [P&L%]
⚠️ Near-trigger: [TOP_ALERT or 'None']
🎯 Top action: [one line]
"
```

## Save to Vault

```bash
mkdir -p ~/Vault/Claude-Code/01-analysis/crypto
```

Save full report markdown to:
`~/Vault/Claude-Code/01-analysis/crypto/$(date '+%Y-%m-%d')-daily-report.md`

## Output

Confirm: Telegram sent + Vault path.
