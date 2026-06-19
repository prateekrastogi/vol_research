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

## Allocation sensitivity — is it knife-edge? (No.)
Sweeping the weights (US, 2004–25; index SPY +12.0%):

| growth/beaten/gold | IRR | maxDD | ret/DD | vs index |
|---|--:|--:|--:|--:|
| 100/0/0 *(overfit corner)* | 16.6% | −34% | 0.48 | +4.7% |
| 60/30/10 | 14.8% | −28% | 0.54 | +2.9% |
| **50/30/20** | **14.2%** | **−26%** | **0.55** | **+2.2%** |
| 50/20/30 *(best risk-adj.)* | 14.1% | −25% | 0.56 | +2.2% |
| 0/100/0 *(beaten only)* | 10.9% | −34% | 0.32 | −1.1% |
| 0/0/100 *(gold only)* | 9.3% | −25% | 0.37 | −2.7% |

- **Every blend with ≥40% growth beat the index (+1.6 to +4.7%) — the edge spans a wide range, not a single point.**
- **More growth → more return *and* more drawdown; more gold → less of both.** Pure-gold and pure-beaten *underperform* the index — **the growth sleeve is the return engine; gold/beaten convert it into a better risk-adjusted result.**
- **The best return-per-drawdown is a *balanced* blend (~50/20/30), not a corner.** Maximizing raw return pushes to 100% growth, which has the *worst* risk-adjusted ratio. Don't optimize to a corner.
- India is even flatter (every blend beat by ~+2.4%), but partly because NiftyBees large-cap was a *weak benchmark* (Next-50 and gold both independently beat it 2009–25).

## Why ETFs, not single stocks (don't add "top-30 names")
Replacing the ETF sleeves with individual large-cap stocks **massively raises crash risk** for no reliable return gain:

| | US stocks (18) | US sector ETFs (8) | India stocks (28) | India ETFs |
|---|--:|--:|--:|--:|
| avg worst crash | **−78%** | −59% | **−73%** | −39% |
| fell >50% | **18 of 18** | 5 of 8 | **27 of 28** | 0 |
| fell >70% | 12 of 18 | 2 of 8 | 17 of 28 | 0 |
| fell >90% | 2 (MU −98%) | 0 | 4 (TataSteel −98%) | 0 |

- Single names routinely crash **50–98%**; sector/broad ETFs cap at ~−40 to −60% and **never go to zero.**
- The stock-sleeve backtest *looks* better (US IRR +20.7%) — that is **pure survivorship bias** (yfinance keeps AAPL/MSFT, deletes the bankruptcies). A real top-30-of-2005 list held names that fell ~100% and never recovered. In India the stock sleeve added **~0% extra return** for all that crash risk.
- Backed by **Bessembinder (2018):** the modal single-stock lifetime return is **−100%**; ~4% of firms create all net wealth. **J.P. Morgan:** ~40% of stocks suffered a *permanent* 70%+ crash — worst in **Tech/Telecom/Energy**, i.e. exactly the "beaten-up sector" names. **Keep every sleeve in ETFs.**

## What the research says (literature check)
| Claim | Verdict | |
|---|---|---|
| Gold/diversification cuts drawdown & raises Sharpe | **AGREE** | but via *risk reduction, not return* |
| Lower-drawdown mix beats 100% equity on *Sharpe* | **AGREE** | wins risk-adjusted, loses on raw CAGR |
| Heuristic 50/30/20 beats optimizing weights | **AGREE** | optimizing overfits; 1/N hard to beat |
| Use ETFs, not single stocks | **AGREE** | single names raise the crash/permanent-loss tail decisively |
| **Growth/QQQ tilt is a durable return edge** | **DISAGREE** | regime/re-rating, *not* a premium — value is the documented one |

- **Gold** — WGC: a 5% sleeve adds ~+0.1pp return but cuts vol/drawdown. Erb & Harvey (*Golden Dilemma*), Dimson-Marsh-Staunton: gold's long-run **real return ≈ 0–1.5%/yr**. Academic optima sit at **2–6% gold**, WGC 2.5–10%, BIS "low single digits" — **so 20% here is high; trim toward ~10% and treat gold as insurance, not a return source** (the high in-sample gold weights are exactly the bias to avoid).
- **Growth tilt** — no "growth premium" exists; the documented premium is **value** (Fama-French). Arnott/AQR/FTSE Russell decompose 2010–24 growth outperformance as **multiple expansion (re-rating)**, which mean-reverts. QQQ fell ~83% in 2000–02 and took 12–15y to recover. **This is the strategy's most fragile, regime-dependent leg.**
- **Weights** — DeMiguel-Garlappi-Uppal (2009): no optimizer beat naive 1/N out-of-sample; Michaud: optimizers are "estimation-error maximizers." **The round-number heuristic is correct; don't fine-tune to the backtest.**

## How this compares to value & momentum investing
The growth tilt is *regime beta*, not a premium. The two genuine documented return premia are **value** and **momentum** — so how do they stack up at beating the index (DCA monthly + hold, long-only factor ETFs)?

| Approach | 2004–25 IRR (maxDD) | 2013–25 IRR (maxDD) | vs index |
|---|--:|--:|--:|
| Growth tilt (QQQ) | 16.3% (−37%) | 18.9% (−30%) | +4.6 / +5.3% |
| **Momentum (MTUM)** | *n/a pre-2013* | 16.0% (−30%) | **+2.4%** |
| **Hybrid 50/30/20** | 14.1% (−27%) | 15.9% (−26%) | +2.3% |
| Index (SPY) | 11.7% (−39%) | 13.7% (−31%) | — |
| **Value (VTV large)** | 10.1% (−42%) | 11.4% (−33%) | **−1.6 / −2.3%** |
| Value (small, IWN/VBR) | 8.5% (−50%) | 9.4% (−42%) | −3.3 / −4.3% |

- **Value *lagged* the index (−1.6 to −4.3%) with deeper drawdowns** over the last 15–20y — the "**value winter**" (2007–2020), the same re-rating regime that made growth/the hybrid look good. A retail long-only value-ETF buyer *underperformed*.
- **Momentum (MTUM) beat the index by +2.4%** — the one classic factor that did in the modern sample, and the same margin as the hybrid — but at the *same* −30% drawdown (no risk reduction), with **crash risk** (2009 long-short momentum crash ≈ −70%) and the **highest turnover** (tax/cost drag, brutal under India's STT).
- **The hybrid matched momentum's excess at *lower* drawdown** (−26% vs −30/−33%), because its edge is *diversification*, not a premium.

**Regime caveat:** 2004–25 is value's *worst* century-era and growth/momentum's best — the table flatters the latter. **Long run, value beat growth ~+3–5%/yr (Fama-French HML, 1926–2007)** — a real derived edge, but earned long-short, requiring decade-long patience (you just saw a 13-year drought) and diluted in long-only ETFs. **Momentum has the highest long-run risk-adjusted premium** of the classics. And the textbook winner is to **combine them**: Asness-Moskowitz-Pedersen ("Value and Momentum Everywhere") show value and momentum are *negatively correlated*, so a value+momentum blend has a far higher Sharpe than either alone — the same "uncorrelated engines take turns" logic as the hybrid, but powered by two genuine premia.

**Verdict:** momentum is the factor that most reliably beats the index *on return* (costs you turnover/crash risk); value is the deepest-documented but is in a drought and recently *lost*; value+momentum combined is the principled return play; the hybrid trades a slightly-less-certain return edge for a **durable drawdown reduction** none of the single factors give.

### Does combining value+momentum beat the index *and* the hybrid? (2013–25)
| strategy | IRR | maxDD | vs SPY | vs hybrid |
|---|--:|--:|--:|--:|
| Hybrid 50/30/20 | 15.9% | −26% | +2.3% | — |
| Val+Mom+Growth ⅓ (rebal) | 15.6% | −29% | +1.9% | −0.3% |
| Momentum alone (MTUM) | 15.5% | ~−30% | +1.9% | −0.4% |
| **Value+Mom 50/50 (rebal)** | **13.7%** | −31% | **+0.0%** | **−2.2%** |
| Val+Mom+Gold 40/40/20 | 13.6% | −27% | −0.1% | −2.4% |
| Index (SPY) | 13.7% | −31% | — | −2.3% |
| Value alone (VTV) | 11.4% | −33% | −2.3% | −4.5% |

**No — the combo *tied* the index (+0.0%) and *lost* to the hybrid (−2.2%).** Momentum *alone* (+1.9%) beat the blend; value was dead weight that dragged it to the index, and the pair didn't even cut drawdown (−31% ≈ index — value & momentum both fell in 2020 & 2022). The combo only matched the hybrid when *growth* was added (regime beta, not the pairing). **Caveat:** 2013–25 is value's worst century-era and the *only* testable window (MTUM born 2013), so it's rigged against the pairing. Asness's value+momentum edge is a **multi-decade, global, largely long-short, risk-adjusted** result — real, but it shows up over a full cycle (incl. value's good years), not in a long-only slice of value's drought; net of turnover/tax (India STT) it erodes further. Bottom line: a documented edge you can't bank on in any given decade — *this* decade it underperformed the simple hybrid.

## Honest caveats (sharpened by the literature)
- **The durable, research-backed win is the lower drawdown / higher Sharpe** (−22% to −26% vs index ~−30% to −39%) — pure diversification, robustly supported. *That* is what you can rely on forward.
- **The +2%/yr *return* excess is largely regime and likely fades.** Three of its sources are suspect: gold (risk-reduction dressed as return; its real return ≈0%), the growth tilt (re-rating that mean-reverts — *not* a premium), and a rebalancing artifact. **Forward-honest: expect ~0.5–1.5% return excess, not 2%** — plus the reliable drawdown reduction.
- **20% gold is above every academic optimum (2–6%)** — the backtest *likes* gold only because of its 2000s/2010s run (in-sample bias). Treat gold as insurance; ~10% is the literature-defensible weight (15–20% only if you explicitly want a bigger crash cushion and accept the CAGR drag).
- **The growth/QQQ sleeve is the fragile leg** — it carries the return *and* the regime risk (an 83%, 12–15-year drawdown is on its record). Don't lever it or assume it keeps leading.
- **The grid "optimal" (100% QQQ / 100% gold) is overfit** — use the fixed heuristic blend, never the optimum.
- **India sample is shorter (2009–25)**, lacks sector breadth, and its benchmark (large-cap NiftyBees) was weak vs Next-50+gold — treat India as more fragile than the US 21-year result.
- Single path per market; ~20 years; survivorship-clean only on the ETF version; no leverage.

## Deployable recipe
1. **Monthly:** allocate savings to a growth-tilted ETF blend — **~50% growth / 30% most-beaten sector / ~10–20% gold** (10% = literature-optimal; 20% = bigger crash cushion, lower CAGR); buy; **never sell.**
2. **US:** QQQ + most-beaten Select-Sector SPDR + GLD. **India:** JuniorBees (or a broad/growth ETF) + most-beaten available ETF + GoldBees (or SGBs).
3. **Always ETFs, never individual stocks** — single names add 50–98% crash + permanent-loss risk you can't capture in advance (see above).
4. **Don't fine-tune the weights to the backtest** — the round-number heuristic is the robust choice; optimizing overfits.
5. Optional light **annual rebalance** toward targets (small rebalancing bonus, caps QQQ drift, at the cost of trimming winners).
6. Expect **~+0.5–1.5%/yr return over index-DCA *plus* materially lower drawdown** — a real *risk-adjusted* improvement, not a home run, and one whose most reliable payoff is lower risk.

## Where this sits in the investigation
Across the whole study, **every active equity strategy lost to buy-and-hold except this one**: directional timing, single-stock dip-buying (value traps), beaten-up rotation (sells winners), sell-at-prior-high (cash drag + exit cap) — all underperformed. The hybrid wins because it is **buy-and-hold of a diversified ETF tilt** — it keeps the things that work (time in market, let winners run, no value traps) and adds a structural drawdown cushion. **The literature's verdict, confirmed by the backtests: the edge is diversification (durable, risk-reducing), *not* the growth tilt (regime-dependent return). Bank on the lower drawdown; treat the extra return as a bonus that may fade.**
