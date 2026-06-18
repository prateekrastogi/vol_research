# Volatility Strategies — Positive-Edge Candidates

*Companion to [README.md](README.md) (the Sinclair / Bennett synthesis). This long-volatility expression showed a **positive edge** in backtesting on real NIFTY and US S&P index data. High-level overview only — entry signals, thresholds, and parameters are intentionally left out.*

---

## Intraday opening-momentum long gamma (index)

**What it is:** After a strong, decisive directional move early in the session, buy a long-gamma index options position, hold it within the session, and exit by the close.

**Edge thesis:** A strong directional opening move tends to continue intraday (momentum / volatility clustering). Trading intraday-only sidesteps the overnight and multi-day time decay that makes day-held long vol bleed. The edge is in *selecting* active / trending sessions — traded indiscriminately every day, long vol loses to the variance risk premium and decay.

**Payoff profile:** low hit-rate, **positive skew** — many small losses, occasional large wins. Size small; judge over many trades with downside-aware metrics (Sortino / Calmar), not win-rate or Sharpe alone.

### Structure: straddle vs slightly-OTM strangle
- **ATM straddle** — more gamma near the money; larger absolute P&L per contract, best on the most extreme moves.
- **Slightly-OTM strangle** — cheaper premium, less capital at risk; roughly the same absolute P&L on the selected big-move sessions for materially less capital → **higher return on capital (ROI)**, scaling to more size at equal capital. More binary (the move must reach the strikes) and more dependent on the selection being right. Its ROI edge held up even after a realistic volatility-skew adjustment.

### Hedged vs unhedged (the key choice)
- **Unhedged — the positive-edge expression, and the only one found here.** The thesis is *directional continuation*, so holding the gamma unhedged keeps the full move. This was the sole sleeve that showed positive expectancy in testing.
- **Hedged is NOT a positive-edge play.** A sparse-band delta hedge converts the position into a realized-variance harvest, which in testing was **negative expectancy in every bucket** except one tiny, noisy variance-selected sample (≈breakeven). On choppy sessions the hedged sleeve loses *less* than the unhedged sleeve — but "less negative" is loss-mitigation, not an edge.
- **Therefore the rule is: trend → unhedged (be long); chop → stand aside (don't be long vol at all).** Delta-hedging is only a way to *reduce the bleed* if you are already long into a choppy tape — it is not a reason to put a position on, and it does not turn a choppy session into a profitable one.

## Scope — where it holds, where it doesn't

- **Holds: short-dated (weekly / near-expiry) INDEX options.** High intraday gamma plus tight spreads are what let the momentum capture pay. This is the only setting where a positive edge appeared.
- **Did NOT hold: monthly-expiry options held intraday.** A longer-dated option carries too little intraday gamma to capture the move before vega decay, spread, and theta erode it — every structure and hedge variant lost across the test, and the momentum selection added no edge.
- **Did NOT hold: single stocks (e.g. NASDAQ-100 names) intraday.** Wide single-name option spreads plus the same low-gamma problem on monthly expiry sank all four combos (roughly −9% to −10% ROI), with momentum selection providing no rescue.
- **Both conditions are required — *short-dated AND index*.** Drop either (go to monthly, or to single names) and the edge disappears. This boundary is structural (a monthly option simply has little intraday gamma), so it is robust to the pricing assumptions.

## Status
Validated on **modeled options priced off real index paths** (real NIFTY and S&P data, real VIX / India VIX, full transaction-cost stack including a realistic skew), holding up on a ~500-session sample. **Pending confirmation on real option-tick data** (real spreads and smile) before live deployment.
