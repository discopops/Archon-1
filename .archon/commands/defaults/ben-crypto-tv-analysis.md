---
description: TradingView chart analysis per holding + opportunity scan using live MCP tools
argument-hint: (no arguments — reads /tmp/cs_portfolio.json from portfolio-data node)
---

# Crypto Daily Report — TradingView Analysis

**Workflow ID**: $WORKFLOW_ID

---

## Load portfolio data

```bash
cat /tmp/cs_portfolio.json
```

Also read executor status and trade plan from the workflow context (provided by executor-status node output).

---

## TradingView analysis protocol

RSI values are already known from the portfolio script (Binance RSI, authoritative). TradingView is used ONLY for EMA levels, chart pattern, and 4H session bias.

### A. Health check (once, before any coin)

Call `tv_health_check`. If unhealthy: skip all TV steps, note "TV unavailable" in all coin entries and proceed.

### B. For each holding ≥ AUD $5 (use tv_symbol from portfolio JSON):

**1. Load symbol:**
```
chart_set_symbol(tv_symbol)
chart_get_state  → verify symbol field matches target
```
If mismatched: retry up to 3×. On 3rd failure: mark coin as TV_UNAVAILABLE, continue.

**2. Get real-time price:**
```
quote_get(tv_symbol)
```

**3. Get chart data (only after symbol confirmed):**
```
chart_set_timeframe("1D")
data_get_ohlcv(count=100, summary=true)     → 100-day high/low/range, last 5 bars
data_get_study_values                        → EMA values (extract short and long EMA)
chart_set_timeframe("240")
data_get_ohlcv(count=20, summary=true)      → 4H session bias (change_pct)
```

**4. Compute derived signals:**
- EMA alignment: price vs short EMA vs long EMA
- 4H bias: positive = bullish, negative = bearish
- 100-day drawdown: `(100d_high - close) / 100d_high × 100`

**5. Apply signal rules (Binance RSI + TV combined):**
- 🟢 P&L+ AND price > short EMA AND 4H positive AND RSI 50–65 → **HOLD / ADD**
- 🟢 P&L+ AND bullish AND RSI >70 → **TRIM 25–50%** (executor auto-triggers at 70/78)
- 🟡 P&L± AND mixed signals OR RSI 40–55 → **HOLD**
- 🔴 P&L- AND price < both EMAs AND 4H negative AND RSI <40 → **REDUCE 50%**
- 🔴🔴 P&L- AND bearish AND RSI <35 → **EXIT full**
- ℹ️ TV unavailable → use Binance RSI + P&L only

---

## Opportunity scan

Use RSI values from portfolio JSON (Binance). Use 100-day drawdown from TV analysis above.

**Short-term watchlist** (1–4 weeks):
BTC, ETH, SOL, BNB, XRP, NEAR, INJ, SUI, ARB, OP, LINK, AVAX, DOT, PEPE, WIF
→ Flag if: RSI 30–45 AND 100d drawdown >15% AND not already >10% of portfolio

**Long-term watchlist** (3–12 months):
BTC, ETH, SOL, QNT, LINK, AVAX, DOT, INJ, NEAR, ARB, RENDER, FET, GRT, UNI, ATOM
→ Flag if: RSI <45 AND price <50% of 100d high

For each flagged coin: check if a buy condition already exists in `daily_trade_plan.json`.
- If yes: note "executor armed at RSI ≤ [X]"
- If no: note "recommend adding to next plan run"

---

## Save output

Write to `$ARTIFACTS_DIR/tv_analysis.json`:

```json
{
  "analysed_at": "ISO-8601 timestamp",
  "tv_available": true|false,
  "holdings": [
    {
      "coin": "BTC",
      "tv_symbol": "BINANCE:BTCUSDT",
      "tv_status": "ok|unavailable",
      "usdt_price": 67200,
      "short_ema": 65100,
      "long_ema": 59800,
      "ema_alignment": "bullish|bearish|neutral",
      "four_hour_change_pct": 1.2,
      "hundred_day_drawdown_pct": 8.3,
      "signal": "HOLD",
      "signal_emoji": "🟡",
      "rationale": "One sentence"
    }
  ],
  "opportunities": {
    "short_term": [ ... ],
    "long_term": [ ... ]
  }
}
```

## Output

Summary: N holdings analysed, N TV unavailable, N opportunity flags.
