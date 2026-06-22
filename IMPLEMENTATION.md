# Implementation — Forecasting Model, Data, Execution & Validation

*Companion to [README.md](README.md) (the synthesis / *why*) and [VOL_STRATEGIES.md](VOL_STRATEGIES.md) (the edges / *what*). This file is the ***how*** — how to actually build, feed, execute, and validate an intraday volatility strategy on NSE. High-level overview; verified ~2026-06 (re-confirm prices/rules before relying on them).*

---

## 0. The one reframe that governs everything

**Forecast accuracy ≠ edge.** The option's implied vol *is* the market's RV forecast, so a good realized-vol model mostly reproduces the consensus. The edge is the **RV-forecast − IV spread, net of the variance risk premium and costs** — and it only appears in narrow, conditional pockets (short-dated, index/liquid, intraday, where IV *under-prices* realized and you can act before the surface re-prices). Build everything to target the **spread**, not the forecast.

---

## 1. Forecasting / signal model

**Core engine (intraday):**
- **HAR-RV, intraday-adapted** — forecast the *next intraday window's* realized vol from intraday RV at multiple lags (e.g. last 15 / 30 / 60 min), not the textbook daily 1/5/22-day form. Computed on the **underlying** (NIFTY futures), 1-min (5-min for the RV components is fine).
- **Lee-Mykland jump flag** — run on the **underlying at 1-min**, *not* on options and *not* at 1-second (microstructure noise → false jumps). Use it as a **regime/feature**, never a reactive entry trigger (a detected jump has already re-priced the option). Position the straddle *ahead* of jump-prone regimes.

**Worth adding (cheap, high-prior, edge-targeting — ~4-6 parameters total, the ceiling):**
| Addition | Why | Evidence |
|---|---|---|
| **Model the RV − IV *spread*** (India VIX / ATM IV as benchmark) | This *is* the edge axis; trade only the residual that beats IV + the regime VRP | the single most important addition |
| **Signed jumps + realized semivariance** (Patton-Sheppard 2015) | downside semivariance predicts vol differently — the leverage effect; relevant to index put-skew | strong |
| **HARQ** (Bollerslev-Patton-Quaedvlieg 2016) | corrects HAR for measurement error in RV (quarticity-weighted lags) | best pure-forecast upgrade |
| **Term-structure / regime conditioning** | compare forecast to IV at the *right horizon*; gate on low IV-percentile / inverted term structure (Johnson 2017 SLOPE — the one documented long-vol-positive case) | targets documented edge |

**Skip — cutting-edge but overfitting / wrong-axis for retail:**
- **Rough volatility** (Gatheral et al.) — payoff is in option *pricing*/smile, not RV point-forecasting; OOS gain over HAR small/contested.
- **ML / deep learning** (LSTM, GBMs) — overfit trap on limited intraday data; OOS gains routinely die net-of-cost.
- **Exotic jump machinery** (Hawkes intensity, cojumps, swapping jump tests) — marginal for a single index.

**Overfitting discipline:** add a feature only if it improves **out-of-sample, net-of-cost Sharpe** — never in-sample R². On a thin edge + small sample, set the bar high (must survive walk-forward *across regimes*). HAR's parsimony is its virtue; don't bolt on complexity to chase intraday R².

---

## 2. Data layer

**Underlying minute/sub-minute (for HAR-RV + Lee-Mykland) and minute option history (for backtest).** Free GitHub/broker-API paths exist for *minute*; historical *tick* is not free.

| Source | Underlying minute | Options minute (incl. expired) | Tick | Free? |
|---|:--:|:--:|:--:|:--:|
| **ICICI Breeze** (`breeze-connect`) | ✅ down to **1-sec** | ✅ **best free, per-strike** | live WS only | ✅ no data fee |
| **Dhan** (`DhanHQ-py`) | ✅ ~5 yr | ✅ expired options + chain/greeks | live WS | ⚠️ **₹499/mo** |
| Zerodha `pykiteconnect` | ✅ | ✅ | live WS | ⚠️ ~₹500/mo data |
| **Fyers** (`fyers-apiv3`) | ✅ underlying | ❌ expired broken/dropped | live WS | ✅ **fully free** |
| Angel One (`smartapi`) | ✅ underlying | ❌ expired broken/dropped | live WS | free |
| `jugaad-data` / `nsepython` | ❌ EOD only | ❌ | ❌ | fragile |

---

## 3. Execution layer

- **QuantConnect — drop for NSE options.** No NSE options data, no F&O routing (Zerodha/Samco plugins = cash equities only). The ~$60/mo buys nothing here. (US SPX/SPY/VIX work on QC, but LRS legally blocks an Indian resident from funding US derivatives.)
- **Best = native broker API: ICICI Breeze** (`breeze-connect`) — free 1-second underlying + per-strike option history for research/backtest.
- **Regulatory (SEBI Feb-2025 retail-algo framework, full enforcement Apr-2026):** static-IP whitelist + 2FA + client-specific keys; **under ~10 orders/sec is exempt** from per-strategy registration (a straddle/strangle is well under). Run on a **fixed-IP VPS**. Use only a SEBI-registered domestic broker (or IBKR's India entity); the global IBKR account / US-options-under-LRS route is legally blocked.

---

## 4. Cost model (mandatory — or the backtest lies)

Build a custom fee model with the full NSE stack; STT is the dominant variable cost and the usual edge-killer:
- **STT: 0.10% of premium on the sell side** (→ **0.15% from Apr-2026**); **0.125% of intrinsic on ITM exercise** → **never let an ITM option expire; square off.**
- NSE txn ≈ 0.03553% of premium; SEBI 0.0001%; **GST 18%** on (brokerage + txn + SEBI); stamp 0.003% on buy; brokerage flat ~₹20/order.
- **Option bid-ask spread** — model it explicitly (tight on NIFTY ATM, wide on OTM/single-stock); it dominates on short-dated, frequent trading.
- Round-trip ≈ **0.8% of premium on an ATM NIFTY option, 30%+ on a cheap far-OTM** (flat fee dominates) — so avoid far-OTM scalping.

---

## 5. Where the edge lives (scope — see VOL_STRATEGIES.md)

- ✅ **Short-dated + index/liquid + intraday + magnitude (not direction) + where IV under-prices** — strong on **BTC daily** (inefficient surface), weaker on **NIFTY** (more efficient + heavy STT/spread drag).
- ❌ Monthly expiry intraday, single-stock intraday, direction prediction (≈coin-flip / mean-reverting), clustering where IV already prices it (equity weekly/30-day = a trap), and leverage (scales risk + pays interest, creates no edge).
- The intraday edge is a **speed edge** — react to elevated/jumping RV *faster than the short-dated IV re-prices*. That is exactly what the HAR-RV + Lee-Mykland intraday signal is for.

---

## 6. Validation discipline

- **Walk-forward, out-of-sample, net-of-cost.** Judge on OOS Sharpe/Sortino after the full cost model — never in-sample R².
- **Benchmark every addition** against (a) a *blind* straddle and (b) the crude opening-momentum baseline. If a feature doesn't beat both OOS net-of-cost, drop it.
- **Validate on real option data** (Breeze/Dhan/TrueData). Every result so far is **BS-modeled off real underlying paths** — directionally informative, not a tradeable edge until confirmed on real option prices/spreads/skew.
- **Evaluate with Sortino/Calmar**, not Sharpe alone (long-vol is positive-skew, lumpy); expect ugly short-window Sharpe.
- **Multiple-testing awareness** — every variant tried raises the overfitting risk on a thin edge.

---

## 7. Build order

1. Custom **NSE cost model** (FeeModel) — first, so nothing downstream flatters itself.
2. **Data adapter** (Breeze research / Dhan live), minute + per-strike options.
3. **Intraday HAR-RV forecast + Lee-Mykland jump flag** on NIFTY futures.
4. **RV − IV spread signal** (de-biased by regime VRP) → straddle/strangle entry/exit.
5. **Walk-forward OOS backtest** on real option data, vs blind-straddle + momentum baselines.
6. If it clears costs OOS → **paper trade** (Dhan/Fyers) → small live on a fixed-IP VPS, SEBI-compliant.

---

## Status & caveats
The strategy work is **research-grade, not yet a validated live edge** — all backtests to date use modeled options; the binding next step is real option data + a walk-forward net-of-cost OOS test. Prices, broker capabilities, and SEBI rules change — re-verify before deploying capital, and consult a SEBI-RIA / FEMA-qualified CA on algo-registration and tax.
