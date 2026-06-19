# Hybrid 3-Sleeve ETF DCA — the one approach that beat the index

*The survivor of the "can an unleveraged ETF strategy beat buy-and-hold?" investigation (see [VOLATILITY_AND_DIRECTION.md](VOLATILITY_AND_DIRECTION.md) for why single-stock / timing / rotation versions all failed). This is the only variant that beat index-DCA by the target margin **with lower drawdown**, and survived a walk-forward test. Backtests use real ETF prices; figures verified ~2026-06.*

---

## The strategy

**DCA your monthly savings, split across 3 sleeves, and hold forever (never sell):**

| Sleeve | Weight | US ticker | India analog | Role |
|---|--:|---|---|---|
| **Growth tilt** | 50% | QQQ | Junior­Bees (Nifty Next 50) / a broad/growth ETF | return engine |
| **Beaten-up mean-reversion** | 30% | most-beaten US sector SPDR that month (XLK/XLF/XLE/…) | most-beaten of the available broad/sector ETFs | sector rebound, diversifier |
| **Gold** | 20% | GLD | GoldBees (or Sovereign Gold Bonds) | crash / inflation hedge, low correlation |

No leverage. Each month's contribution is allocated to the targets; you **never sell** (portfolio drifts toward the growth sleeve over time). "50/30/20" is a sensible default, not a fitted optimum — the result is not knife-edge.

---

## Results (real ETF data, DCA monthly + hold)

| Market | Strategy | IRR | maxDD |
|---|---|--:|--:|
| **US** (2004–25) | SPY DCA (benchmark) | +11.7% | ~−39% |
| | **Hybrid 50/30/20** | **+13.7%** | **−26%** |
| **India** (2009–25) | NiftyBees DCA (benchmark) | +11.9% | ~−30% |
| | **Hybrid 50/30/20** | **+13.4%** | **−22%** |

**≈ +2%/yr excess on both markets, with ~30-40% lower drawdown.**

### Walk-forward (the key validation — fixed blend, no fitting, separate sub-periods)
| Market | Sub-period | Hybrid | Index | Excess |
|---|---|--:|--:|--:|
| US | 2004–2012 | +9.7% | +5.9% | **+3.8%/yr** |
| US | 2013–2025 | +15.0% | +13.3% | **+1.8%/yr** |
| India | 2009–2016 | +11.8% | +7.8% | **+4.1%/yr** |
| India | 2017–2025 | +15.2% | +13.2% | **+2.0%/yr** |

**Positive excess in all 4 sub-periods of both markets** — and crucially via *different* sleeves each era: in the US **2004-12 tech was a lost decade so GOLD carried it; 2013-25 tech boomed so QQQ carried it.** The sleeves take turns leading — diversification doing its job, not a single-regime fluke.

---

## Why it works
- **Growth (QQQ)** supplies the return; **gold** and the **beaten-up-sector** sleeves are lowly/negatively correlated cushions that fire in *different* regimes (gold in risk-off / 2000s & 2019-24; tech in risk-on / 2010s; beaten sectors on their rebound).
- Because the engines are anti-correlated, **at least one tends to be working** — so the edge is robust across regimes, and the **drawdown is structurally lower** than an all-equity index.
- It's **buy-and-hold + DCA** — low turnover, no value-trap risk (ETFs don't go to zero), no cash drag, no selling winners. It avoids every failure mode of the earlier variants.

## Honest caveats
- **Return excess is regime-dependent in *size*** (+1.4% to +4.1% across windows; centre ~+2%). The high end was **gold-driven** (its 2000s bull) — don't assume it repeats. **Forward, expect ~1.5–2.5% excess, not a guaranteed 3%.**
- **The structural win is the lower drawdown** (−22% to −26% vs index ~−30% to −39%) — that's the most forward-reliable benefit (it's diversification, not return-prediction).
- **Component selection has hindsight** — QQQ and gold were *chosen because* they did well; a truly out-of-sample analyst in 2004 might not have picked them. The walk-forward (beating even in tech's lost decade, via gold) mitigates but doesn't eliminate this.
- **The grid "optimal" (100% QQQ / 100% gold) is overfit** — the optimizer just picks the ex-post winner. **Use the fixed blend, never the optimum.**
- **India sample is shorter (2009–25, clean from 2009)** and lacks sector-ETF breadth, so the "beaten-up" sleeve is crude there; treat India as more fragile than the US 21-year result.
- Single path per market; ~20 years; no leverage assumed.

## Deployable recipe
1. **Monthly:** allocate savings ~**50% growth / 30% most-beaten sector / 20% gold**; buy; **never sell.**
2. **US:** QQQ + most-beaten Select-Sector SPDR + GLD. **India:** JuniorBees (or a broad/growth ETF) + most-beaten available ETF + GoldBees (or SGBs).
3. Optional: a light **annual rebalance** toward targets adds a small rebalancing bonus and caps the QQQ drift (at the cost of selling some winners) — optional, not required.
4. Expect **~+1.5–2.5%/yr over index-DCA with materially lower drawdown** — a real *risk-adjusted* improvement, not a home run.

## Where this sits in the investigation
Across the whole study, **every active equity strategy lost to buy-and-hold except this one**: directional timing, single-stock dip-buying (value traps), beaten-up rotation (sells winners), sell-at-prior-high (cash drag + exit cap) — all underperformed. The hybrid wins because it is **buy-and-hold of a diversified, regime-rotating ETF tilt** — it keeps the things that work (time in market, let winners run, no value traps) and adds a modest, regime-robust tilt plus a structural drawdown cushion. The edge is **diversification + a growth tilt**, not market timing.
