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
- **Unhedged — the positive-edge expression for this thesis.** Because the thesis is *directional continuation*, holding the gamma unhedged keeps the full move. This is where the edge showed up in testing.
- **Sparse-band delta-hedged — only for a chop / realized-variance thesis.** Delta-hedging converts the position into a pure realized-variance harvest and *sells the trend away*; on the momentum selection it merely matched or lagged the unhedged sleeve (and on NSE its rehedging cost dragged it further behind). It is preferable only when you expect a high-realized-but-rangebound (choppy) session rather than a trending one.
- **Rule of thumb:** match the hedge to the thesis — **trend → unhedged; chop → hedged.** Mixing them (hedging a trend bet) is the worst of both.

## Status
Validated on **modeled options priced off real index paths** (real NIFTY and S&P data, real VIX / India VIX, full transaction-cost stack including a realistic skew), holding up on a ~500-session sample. **Pending confirmation on real option-tick data** (real spreads and smile) before live deployment.
