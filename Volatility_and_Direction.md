# Volatility & Direction — The Crux (with mathematical backing)

*Companion to [README.md](README.md), [VOL_STRATEGIES.md](VOL_STRATEGIES.md), [IMPLEMENTATION.md](IMPLEMENTATION.md). Why directional/trend bets fail for a retail single-market trader, why diversified CTAs survive, and the reframe to volatility. Empirical figures are from this repo's backtests (modeled options off **real** underlying paths) + the cited literature.*

---

## TL;DR — the crux

**Direction is (near-)unpredictable for a single market over tradeable horizons; *magnitude* (volatility) is the predictable axis.** Returns barely autocorrelate (and *negatively* at short lags = reversal); volatility autocorrelates strongly (clustering). Therefore: **trend-follow volatility, not price direction.** Short-term *directional* trend is dead for retail single-market trading; it survives only as a *diversified, multi-market CTA program* — and that is a **diversification (√breadth) result, not a per-market edge.**

---

## 1. Predictability by horizon

| Horizon | Dominant *directional* effect | Strength |
|---|---|---|
| seconds–minutes | microstructure continuation (order flow) | tiny; HFT-only, gone after costs |
| **1 day – 1 month** | **short-term REVERSAL** (Jegadeesh 1990; Lehmann 1990) | weak (~52% the wrong way for trend) |
| **3 – 12 months** | **MOMENTUM** (Jegadeesh-Titman 1993; TSMOM, Moskowitz-Ooi-Pedersen 2012) | strongest, but ~53–55% hit, **cross-sectional / diversified** |
| 2 – 5 years | long-term reversal + valuation mean-reversion (De Bondt-Thaler; Campbell-Shiller) | weak-moderate, very slow |

Momentum is a **3–12-month, cross-sectional / multi-asset** phenomenon. **Sub-month is reversal territory.** So a <1-month directional *trend* bet is fighting the dominant short-horizon effect.

---

## 2. The empirical crux — the autocorrelation asymmetry

Measured on NASDAQ-100 names (non-overlapping h-period returns, this repo):

| Horizon | Return autocorr (direction) | Vol autocorr (magnitude) |
|---|--:|--:|
| 1 week | **−0.059** | +0.179 |
| 2 weeks | **+0.003** | +0.143 |
| 1 month | **−0.005** | +0.091 |

Cross-market clustering correlations: BTC **+0.39 to +0.46**, equities **+0.26 to +0.32**.

$$\underbrace{\rho_{\text{return}}\approx 0}_{\text{direction unpredictable}} \qquad\qquad \underbrace{\rho_{\text{vol}}\gg 0}_{\text{magnitude predictable (clustering)}}$$

**Magnitude is forecastable; direction is not.** This is *the* reason to trade direction-neutral structures (straddles/strangles).

---

## 3. Direct evidence on NIFTY (our tests)

- **Quarterly direction:** base rate 64% up (pure bull *drift* = beta). Conditioning adds nothing — trailing-up 65% vs trailing-down 62%; and **below the 200-DMA the forward return was *higher* (+3.1%) than above (+2.5%) = reversion, not trend.**
- **12-month TSMOM, single index (19y):** long/short **Sharpe 0.07, CAGR −0.7%** vs buy-&-hold **0.55 / +9.6%**. After a 12-month *down* period the next quarter averaged **+8.9% (77% up)** vs **+2.1%** after an up period — **strong mean reversion.** Buy-&-hold beat every timing variant.
- **Cross-sectional momentum, NIFTY constituents (2019-26):** long-short **Sharpe −0.09, CAGR −2.8%**; long-only winners (1.38) **underperformed** equal-weight benchmark (1.55). No edge beyond beta.
- **Intraday:** after a >1% opening move, continuation over the next hours was only **44%** (40% after >2%) — i.e. *mean reversion*. The opening-momentum straddle "worked" on **magnitude, not direction**; the ratio-backspread "edge" was a **bull-beta artifact**.

Every single-market directional-trend test came back ≈ beta-or-worse.

---

## 4. Why diversified CTA short-term trend survives — the math

A single market's short-term trend signal has a near-zero per-bet skill, yet a broad CTA turns it into a real program. Two equivalent derivations.

### 4a. Grinold's Fundamental Law of Active Management
$$IR \approx IC \cdot \sqrt{BR}$$
- **IC** (information coefficient) ≈ skill per bet ≈ corr(forecast, outcome) ≈ **0.03–0.05** for short-term trend.
- **BR** = independent bets/year = markets × rebalances/year.

Weekly trend × ~50 markets ≈ 50 × 50 = **2,500** bets ⟹ $IR \approx 0.04\times\sqrt{2500}=2.0$.
A single market (BR ≈ 50) ⟹ $IR \approx 0.04\times 7 \approx 0.28$ — untradeable noise. **The √BR (breadth) term is the whole story.**

### 4b. Portfolio-Sharpe scaling (makes the ceiling explicit)
N markets, identical per-market Sharpe $S_1$, average **cross-strategy** correlation $\rho$, equal weight:

$$\text{Var}_{port}=\sigma^2\!\left[\rho+\frac{1-\rho}{N}\right]\quad\Rightarrow\quad S_{port}=\frac{S_1}{\sqrt{\rho+\dfrac{1-\rho}{N}}}\;\xrightarrow{N\to\infty}\;\frac{S_1}{\sqrt{\rho}}$$

Expected return per market is **unchanged** — diversification only crushes the **variance**. With $S_1=0.25$:

| N | ρ = 0.10 | ρ = 0.05 |
|---|--:|--:|
| 1 | 0.25 | 0.25 |
| 10 | 0.57 | 0.66 |
| 50 | 0.73 | 0.98 |
| ∞ (= $S_1/\sqrt{\rho}$) | **0.79** | **1.12** |

A **0.25 → ~0.8–1.1 Sharpe** (~3–4× boost). The idiosyncratic noise in each market's bet averages away; the small **common trend premium** survives with far higher signal-to-noise.

### 4c. Why *trend* diversifies unusually well
Trend **strategies** have low pairwise correlation ($\rho \sim 0.05$–$0.2$) even across correlated **markets**, because the signal fires at different times/directions per market (gold up while JPY down while bonds chop). Low $\rho$ + a small persistent common premium (behavioral under-reaction) ⟹ the formulas deliver a real Sharpe.

### 4d. Why this does NOT transfer to retail single-market
The result is **gross of costs** and **requires the breadth**:
- **Costs scale with N × turnover.** Replace $\mu\to\mu-c$: if per-market **net** edge $\mu-c\le 0$ (which retail STT/spreads at sub-month frequency easily cause), **no amount of √BR helps** — you diversify a pile of negative-net strategies. The boost needs **institutional-low costs**.
- **Breadth must exist.** N ≈ 50–100 *uncorrelated global futures* is what makes √BR large and $\rho$ small. A retail Indian trader can't access that universe (FEMA/LRS, broker reach) and is stuck at N ≈ 1–3 *correlated* instruments ⟹ $S_{port}\approx S_1\approx 0.25$ — back to noise.

### 4e. The ceiling is also the catch — "trend crashes"
$S_{port}$ tops out at $S_1/\sqrt{\rho}$, **not infinity**: the residual correlation $\rho$ is irreducible. That $\rho$ is the common trend-factor risk — occasionally **all markets whipsaw/reverse at once** (e.g. Q1-2009 momentum crash, sharp risk-on reversals), the diversification vanishes exactly when needed, and the program takes a correlated drawdown. It is the mathematical price of the √BR boost.

---

## 5. The reframe — trend-follow *volatility*, not *direction*

From §2: $\rho_{\text{return}}\approx 0$ but $\rho_{\text{vol}}\gg 0$. **Volatility trends (clusters) sub-month; price direction does not.** So the tradeable short-horizon "trend" is in *how much* it moves, not *which way* — i.e. **long gamma / straddles**, not directional structures.

Caveat (the binding one): vol-clustering is only *profitable* where the option surface **under-prices** it (the market prices the most famous stylized fact). Empirically: **BTC daily under-prices it (tradeable); NIFTY mostly prices it (thin, cost-drag).** See [VOL_STRATEGIES.md](VOL_STRATEGIES.md).

---

## 6. Practical conclusions (retail, NSE)

1. **Sub-month *directional* trend: dead** for a retail single-market trader — reversal-dominated, weak/crowded, cost-eaten, regime-fragile; confirmed by every NIFTY/intraday test above.
2. **Momentum (3–12mo)** is real but only as **cross-sectional stock long-short** or **diversified multi-asset TSMOM** — *not* expressible as a single-index option bet, and the diversified version needs market breadth + low costs retail lacks.
3. **Directional option structures** (ratio backspreads, ITM directional, leveraged/MTF longs) have **no edge beyond beta** at these horizons — apparent "edges" were bull-market beta artifacts.
4. **Trade magnitude, direction-neutral** (straddles/strangles), short-dated, where IV under-prices realized — the only short-horizon axis with a real, if thin, edge.

---

## Caveats
Single-market, mostly 2007–2026 (bull-skewed) samples; backtests use modeled options off **real** underlying paths (not real option prices). Cross-sectional momentum & diversified TSMOM are real, well-documented effects — **not refuted here**; only their *single-market, retail, sub-month* application is. The math (§4) is standard portfolio theory; the IC/ρ/$S_1$ figures are illustrative — the *structure* (√breadth, variance reduction, the $1/\sqrt{\rho}$ ceiling, the $\mu-c$ gate) is the robust part.
