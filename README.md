# Long Volatility for a Retail NSE / QuantConnect Trader
### A verified synthesis of Euan Sinclair, *Volatility Trading* (2e, 2013) and Colin Bennett, *Trading Volatility* (2014)

*Prepared for a retail trader who builds on QuantConnect (LEAN) and trades optionable stocks & indices on NSE India. Current as of 2025-26.*

---

## 0. How this was produced (method & provenance)

- **Sources fully ingested.** Both books extracted to text (Sinclair 298 pp / ~92k words → `text/sinclair.txt`; Bennett 317 pp / ~93k words → `text/trading_vol.txt`).
- **Extraction (44 agents).** Every section of both books read for long-vol content → **593 raw insights** → clustered to **34 candidate strategies**, each carrying *supporting* and *conflicting* citations, plus 8 grounding dossiers on the NSE/QC environment. → `cands.json`.
- **Adversarial verification.** Each candidate run through a refute / NSE-applicability / regime-robustness panel, plus 6 web fact-checks of the disputed India numbers. **34/34 candidates verified** (20 full 3-lens panels, 4 two-lens, 10 consolidated panels) + 6 fact dossiers. → `verified.json`, `verified_compact.json`, `topup_rulings.json`.
- **Honesty note.** Indian-market history is short and several edges rest on single recent studies; treat magnitudes as directionally solid, not gospel. The verification *confirmed every tier placement and produced no contradictions* — the only upgrades were two practices elevated to Tier 1 (the QC proof-of-concept gate, §F; and the regime log, §G).

**How to read this:** §A–B are the foundation (the market you trade and the physics of long vol). §C is the core program. §D conditional add-ons. §E what to *not* do. §F–G the platform reality and the monitoring discipline. §H a concrete starter blueprint. §I caveats. The Appendix is the full 34-candidate verdict ledger.

---

## 1. TL;DR — the one thing to internalize

Both books agree on the prosecution's case: **implied vol is structurally above realized (the variance risk premium), so naïvely being long vol _bleeds_.** Verified for India: **NIFTY IV exceeds subsequent realized vol on ~75% of days, by ~+1.2 vol points on average** (smaller than the US ~+4).

The defense — your actual edge — is that the premium is **conditional**: it narrows and *inverts* in panics, is **much smaller/absent in single stocks**, and is overwhelmed by **forecastable realized-vol bursts** (genuine fat-tail events, mispriced jumps, cheap-percentile regimes).

> **Actionable long vol = harvesting those specific windows while paying the least possible carry to wait — on the one instrument that actually works for you (NIFTY index options), sized far below Kelly, with a cost model honest enough that your backtest doesn't lie.**

---

## A. The board you actually play on — 6 verified India constraints

These bend every strategy below.

| Constraint | Verified reality (2025-26) | Consequence for long vol |
|---|---|---|
| **Variance risk premium** | NIFTY IV > realized **~74.9% of days, mean +1.2 vol pts**; inverts ~25% of days and into shocks. India VIX mean-reverts (level ~13–17 post-2014; VRP AR(1)≈0.79, ~3-day half-life). Single-stock VRP smaller & noisier. US analogue ~+4 vol pts. | You overpay ~75% of the time. Every entry must clear **IV + the premium**, not just IV. |
| **Cost stack** | STT **0.10%** on sell-side premium (since Oct-2024; **→0.15% from Apr-2026**) + **0.125% on ITM intrinsic at exercise**; NSE txn 0.03553%/side; SEBI 0.0001%; GST 18% on (brokerage+txn+SEBI); stamp 0.003% buy; brokerage flat **₹20/order**. **Round-trip ≈0.8% on an ATM option, but 30%+ on a cheap far-OTM** (flat fee dominates). | Carry drag is brutal on cheap options. **Never scalp far-OTM; never let ITM expire.** A custom fee model is mandatory. |
| **Lots / expiries** | Jan-2026 lots: **NIFTY 65, BANKNIFTY 30, FINNIFTY 60, MIDCPNIFTY 120** (~₹16–17 lakh notional each). **Weeklies: NIFTY only** (BANKNIFTY/FINNIFTY/MIDCPNIFTY weeklies discontinued Nov-2024). NIFTY weekly expiry = **Tuesday** since Sep-1-2025. | Short-dated gamma exists **only on NIFTY**. Everything else is monthly → extra diffusive theta. Coarse lots force chunky sizing. |
| **India VIX** | Index since 2008; **futures de facto dead** (~last trade Feb-2016); **no options, no ETFs ever**. | **No tradable long-vol product exists.** Long vol must come from NIFTY/stock options. India VIX is a *gauge* only. |
| **QuantConnect** | **No NSE options/index/futures data** (only TrueData cash equities, ~2,053 names from 2010). Zerodha & Samco plugins route **equity/commodity only — no F&O**. Custom options data does **not** register as native Option securities (no auto-Greeks, can't order). | QC is an *engine without ammo* for NSE options. See §F for the three architectures and the mandatory POC gate. |
| **Skew & events** | Steep negative **put skew** (25Δ risk-reversal ~+3 to +6 vol pts, steeper in stress); single-stock skew shallower; term structure contango ~80%, inverts in stress. **Budget = IV crush** (VIX down 15/15 yrs, ~−9%); **RBI = mild crush**; **elections = IV explosion** (Jun-4-2024: VIX +27.7%, NIFTY −6%); single-stock earnings: implied move > realized, 35–50% post-event crush. | Most "events" are short-vol traps. Only genuine fat-tails (elections, surprises) reward holding long gamma *through*. |

---

## B. The five laws of long vol (engine from both books; claim ↔ counterclaim resolved)

1. **The edge is `½·S²·Γ·(σ²ʳᵉᵃˡⁱᶻᵉᵈ − σ²ⁱᵐᵖˡⁱᵉᵈ)` per unit time** (Sinclair Ch.1). You win only when realized variance beats the implied you paid. **Verified correction:** the gate is *not* `forecast RV > IV`; it is **`forecast RV > IV + the structural premium`** (~+1.2 vol pts NIFTY index; ~0 single-stock). The literal rule loses to the VRP.

2. **Carry vs convexity.** You pay theta (and "skew theta" on the put wing) every quiet day to own rare convex/jump payoffs. **Resolved tension:** long vol is *paid beta, not free alpha* (Sinclair Ch.11; Bennett 3.2) — **but** the premium goes *negative* above ~VIX 50, so long vol pays precisely in panics. It is insurance you are occasionally paid to hold.

3. **The gamma-scalp engine** (Sinclair Ch.6–7; Bennett 2.1): delta-hedge to flat, harvest `½Γ·dS²`, P&L scales with the *square* of moves (2× move = 4× profit). **Verified correction:** the vol-neutral carry invariant is **per unit _vega_ ≈ (realized − implied) vol points** — *not* per dollar-gamma (dollar-gamma carry scales with σ).

4. **Forecast-correct-but-still-lose** (Sinclair Ch.7; Bennett 3.5): discrete-hedging path noise ≈ `vega·σ·√(π/4N)`, independent of edge size; jumps can't be hedged away by frequency. → Survival is via *repetition + sizing*, never single outcomes — **but only if trade selection has positive edge**, else repetition converges to the VRP loss.

5. **Regime & timing dominate.** Vol mean-reverts and clusters; the premium is largest when IV is low; events ramp-then-crush IV. Favorable windows are narrow and partly foreseeable. **Buy-and-hold is the wrong default.**

---

## C. Tier 1 — the verified, NSE-feasible core program

### C1 · When to be long (entry, timing, regime)

- **Master entry rule (de-biased).** Go long vol only when forecast RV over the option's life **exceeds IV by more than the typical premium** (~+1.2 vol pts NIFTY; ~0 single-stock) **and** you can name the catalyst. "IV looks cheap" is not a trade.
- **Volatility cone — a *necessary-not-sufficient* gate.** Buy only when IV is in the **bottom ~20–25th percentile** of the tenor-matched cone **and** the IV-minus-RV-cone-median spread is also low (cheap vs *forward* RV, not just history). Use **Hodges-Tompkins** overlapping-data correction; cap lookback ~8 months. India VIX ~13–14 is the practical "cheap" zone. *(Sinclair's cone is actually sell-biased; low percentile alone ≠ buy.)*
- **Regime as a veto, not a trigger.** "Low-VIX / above-200-DMA" = **hard veto on passive long vol**. Do **not** treat "below-200-DMA + VIX rising" as a mechanical buy (curve-fit; walks you into the post-crash window where dealers force-sell vol, Bennett 3.4).
- **Events done correctly (biggest India-specific fix):**
  - **Budget / RBI / single-stock earnings = IV-crush traps.** Trade the **ramp** (buy ahead, **exit before** the print); don't hold through.
  - **Genuine fat-tails (elections, confidence votes, surprises) = hold long gamma _through_.** (2024 result day: VIX +27.7%, NIFTY −6%.)
  - Single-name earnings: go long *only* if the **implied jump is cheap vs the stock's own realized-jump history** (decompose front-vs-forward vol; index-adjust to strip macro slope first). Usually it isn't.

### C2 · What to buy (structure, strike, instrument)

- **Instrument: NIFTY 50 index options for the core, full stop.** Cash-settled, deepest book, paise-wide, the only weekly. **Drop single-stock long vol for retail** — physical settlement (delivery blow-ups), monthly-only (theta drag), thin (~1.2% spreads), no premium discount worth the friction. Single names only as *defined-risk, hold-to-payoff* event bets among the top ~10–15 liquid F&O large-caps, **always squared off before monthly expiry**.
- **Structure: long ATM straddle or slightly-OTM strangle.** The only retail-accessible direction-neutral long-gamma/vega vehicle (no var swaps, no VIX on NSE). Strangle spreads gamma over a wider range at lower vega. Zero-delta straddle strike sits slightly above spot: `K = S·e^((r+σ²/2)T)`.
- **Strike: near-ATM (Δ 0.45–0.60), not far-OTM.** Far-OTM long calls have the *worst* expected returns (lottery premium) *and* the worst NSE costs (flat fee = 30%+ of a cheap premium, ~75% spreads). Reserve far-OTM strictly for hold-to-payoff tail bets, never scalps.
- **Execution: rest passive limit orders on the bid/offer** for the long-gamma core; prefer liquid ATM strikes.

### C3 · How to run it (hedging mechanics)

- **Delta-hedge with NIFTY/BANKNIFTY futures** (dividend-clean, no stock-loan leg) — carry `(r−q)` is still paid in the basis on every roll; one future ≈ ₹16–17 lakh forces chunky hedging.
- **Don't hedge continuously — use a no-trade band.** Whalley-Wilmott half-width `H = ((3·c·Γ²·S)/(2a))^(1/3)`; rehedge only to the band edge; **widen the band for long gamma**. *(Band width is set by gamma/cost; the "earn the spread on bid/offer" trick does **not** work on tick-wide NIFTY futures.)*
- **Hedge at your _expected_ vol, not implied** — Hull-White minimum-variance delta `Δ_MV = Δ_BS + vega·(dσ/dS)`; with NIFTY's steep negative skew, for long puts `Δ_MV` is *more* negative. Removes a directional bias but reduces leakage, doesn't create edge.
- **Jumps: own them statically, don't dynamic-hedge them.** Flatten delta just before a print and let the long structure take the gap; bound short-leg risk with far-OTM long wings.

### C4 · How to size & survive (this is what keeps you alive)

- **Fractional Kelly — mind the sign.** Anchor `f = (μ−r)/σ²`. For a *naïve always-on* long-vol book μ is **negative** (the VRP) → Kelly literally says *short* vol. So Kelly applies only to your **conditional, edge-positive** program; run **quarter-Kelly**, bankroll = **total net worth** (never broker margin), net the **current repo (~5.5–6.5%)**.
- **Pre-commit a drawdown budget — four frozen numbers.** (1) max cumulative theta-bleed = the rupee loss that liquidates the book, **sized to survive your worst NIFTY calm stretch (~8–12 weeks of ~70–75% loss-days)**; (2) success, (3) respectability, (4) failure P&L levels. Don't cut lots on a normal bleed run; don't add lots after a tail win; revise only on documented regime evidence.
- **No P&L stops — exit on thesis invalidation.** For pure long-premium NIFTY structures, max loss = premium, so "no stop, size small" is clean. *Caveat:* anything with a **short leg** can be **force-squared-off** near expiry (SEBI 2% expiry-day ELM; calendar-margin benefit removed Feb-2025). Keep the long-vol core pure long premium. Distinguish bleed from a regime shift (huge volume + no news + extreme move = you're wrong).
- **Psychology — a 5-line pretrade card** before every trade: (1) RV-forecast vs NIFTY ATM IV / India-VIX percentile [the vol-point edge]; (2) catalyst & why it's a fat-tail not a story; (3) size in lots; (4) bleed budget you pre-accept (down ~3 of 4 days); (5) exit triggers. Log only whether you followed the card; review the *process distribution* monthly, never a single outcome.
- **Evaluate with a 4-metric panel, never Sharpe alone:** Sharpe + **Sortino (MAR=0)** + **Calmar (~1 target)** + rolling Sharpe with its **Lo (2002) standard error `SE = √((1+SR²/2)/n)`**. A negative ~100-day Sharpe with |value| ≤ SE is *not* edge decay and must not trigger a stop. Sharpe alone penalizes the upside vol you're paying for.

### C5 · Costs & execution (graded the single strongest NSE verdict — "holds")

- **Write a custom LEAN `FeeModel`** with STT (0.10→0.15% Apr-2026 on sell premium; 0.125% on ITM intrinsic), NSE txn, SEBI, GST, stamp — or **your backtest shows false positive carry.**
- **Minimize roll frequency, use limit orders, prefer liquid ATM strikes, always square off winners in the market — never let ITM expire** (intrinsic STT + single-stock physical delivery).

---

## D. Tier 2 — conditional / secondary tools

- **Long index skew = deliberately-paid insurance, not alpha.** NIFTY puts bleed skew-theta + VRP in calm; pay only when a jump catalyst is nameable; budget ~1–2% of portfolio/yr. ("Surface re-mark exactly cancels skew theta" is regime-dependent, not an identity.)
- **Tail hedge = election-timed convexity, not a rolling hedge.** A continuously-rolled NIFTY-put hedge bleeds ~28%/yr (1M) in the long Indian calm. Buy when India VIX is bottom-decile *and* a binary catalyst is near; don't add at peak fear.
- **Var-swap "poor-man's log contract" (recast).** Don't build a true 1/K² strip (NSE wings too thin). For spot-robust long gamma into a fat-tail (e.g. an election): a **put-heavy 3–5-strike NIFTY weekly portfolio** with more lots on lower strikes (≈1/K²), delta-hedged with NIFTY futures, accepting it is short the deep tail. Backtest convexity vs a single ATM straddle (custom-data harness, ₹20/order **per leg** + 0.10% STT) before risking capital.
- **Vol-pair / RV (NIFTY vs BANKNIFTY) — weakest RV idea.** Both bank-heavy (shared beta masquerades as a vol spread); NIFTY-weekly vs BANKNIFTY-monthly can't be expiry-matched. If used: monthly-vs-monthly, ATM, long the index nearer the bottom of its *own* IV-percentile; stand down on correlation breakouts.
- **Realized-vol estimator toolkit — use ONE Yang-Zhang series**, not a battery. 21- & 63-session, annualize √250, carry a ±25% error band. Run the full battery *once* offline only to confirm estimators are 0.9+ correlated, then commit to YZ (gap-aware; the estimator the authoritative India VRP study used). Use close-to-close only when P&L is hedged/settled close-to-close.
- **Volume + fundamental confirmation — overlays, never triggers.** Take an entry only when the cheapness gate already fires *and* a volume surge + IV-term-structure inversion confirm a genuine regime shift. Use Sridharan fundamental screens offline only to rank which 1–3 liquid names merit occasional monthly long-gamma; exclude any name inside its pre-earnings IV-ramp.

---

## E. What does NOT work for you (verified refutations / recasts — the honest list)

- **Single-stock dispersion (long single-name vol vs short index)** — **refuted for NSE retail.** Single-stock illiquidity + physical settlement + basket tracking error > the correlation-premium edge. Recast onto **US options** if wanted.
- **Buying single-stock earnings straddles screened by analyst dispersion** — **refuted for NSE.** The screen concentrates you in the *most overpriced* events (richest ramp → biggest crush). The defensible recast is the opposite (sell the crush) — which isn't long vol.
- **Calendar / term-structure spreads as "long vol"** — **refuted (sign error).** A long calendar (long far/short near) is long *vega* but **short gamma with positive theta** — *not* long realized vol — and entering it in backwardation is backwards (a front spike richens the far leg → the harvest is a *short* calendar). For long vol, use near-dated long gamma.
- **Any India VIX product** — **doesn't exist.** Guardrail, not a strategy: long vol on NSE only through options. India VIX is a gauge.
- **Leveraged/inverse ETF vol-drag trades** — no liquid Indian product; informational only. The transferable lesson: verify the *sign* of realized-variance exposure (long gamma pays in chop; short variance bleeds in chop) — don't mistake price-convexity for long variance.
- **Far-OTM scalping, buy-and-hold long vol, single-stock long vol as a continuous program** — all bleed by construction in India.

---

## F. QuantConnect architecture — and the mandatory proof-of-concept gate (Tier 1)

QC is a great options *engine* with **zero NSE options data and no F&O routing.** Three honest paths:

1. **DIY on LEAN (heaviest):** ingest custom NSE options + underlying data, compute Greeks/IV yourself (QuantLib / py_vollib), run the delta-hedge loop in code, attach custom `FeeModel`/`FillModel`. Execute live via **Zerodha Kite Connect API outside QC**.
2. **Split stack:** research/signal in QC on the cash-equity/regime data it *does* have; execute options via Kite/AlgoTest-style tooling.
3. **Pivot to QC-native US (lightest, fully turnkey):** SPX/SPXW + VIX + SPY options (AlgoSeek, full Greeks/chains/fees from 2012) prototype *every strategy above* out-of-the-box; port validated logic to NSE execution.

> **The gate (do this _first_):** Run a **1-expiry proof-of-concept** — ingest one NIFTY weekly (Tuesday) option chain + NIFTY underlying as LEAN custom data, mint contracts via `Symbol.CreateOption`, compute IV/Greeks with py_vollib/QuantLib, attach the custom `FeeModel` (₹20 + 0.10% sell-STT + 0.03553%/side NSE + 18% GST + 0.003% buy-stamp). **If you cannot reproduce traded option prices and a sane delta-hedge P&L within tolerance, abandon NSE-on-QC and run the research on QC-native US SPX/SPY/VIX instead.** Given your constraints, prototyping on US SPX/VIX and trading NIFTY via broker API is the pragmatic default.

---

## G. The regime log — ongoing edge-dissipation monitoring (Tier 1)

A one-page living log, because structural breaks and crowding silently kill long-vol edges:

- **(a) On every SEBI/NSE structural circular, stamp the date and discard any NIFTY long-vol stat estimated across the break** — refit on post-break data only. (The 2024-25 reforms — lot sizes, weekly cuts, Tuesday expiry, expiry-day ELM — already invalidate pre-2024 backtests.)
- **(b) Weekly, log realized NIFTY VRP** (ATM IV − 20-day Yang-Zhang RV) against the +1.2 vol-pt baseline; **add persistent long gamma only when that premium compresses toward/through zero** (cheap), never into a scheduled crush event.
- **(c)** Watch counterparty mix (retail vs professional flow) and whether the event/skew premia are richening (crowding). When a winning trade degrades, diagnose: source gone, execution declined, or counterparty shifted?

---

## H. A concrete starter blueprint

A **conditional NIFTY long-gamma program** — the minimum viable expression of the above:

- **Universe:** NIFTY 50 options only (weekly Tuesday + monthly).
- **Entry gate (all must hold):** India VIX ≤ ~13–14 (bottom-quartile cone) **AND** forecast RV (Yang-Zhang + event calendar) exceeds IV by **> ~2 vol pts** (covers +1.2 VRP + discrete-hedge noise) **AND** a nameable catalyst. Veto if VIX already elevated or a scheduled crush event is imminent.
- **Structure:** long ATM straddle (event/gamma) or slightly-OTM strangle (wider range); near-dated for events, monthly for slow regime bets. Passive limit entries.
- **Hedge:** NIFTY future, Whalley-Wilmott band (wide, long-gamma), MV-delta at forecast vol; **flatten before known prints** and own the gap.
- **Event rules:** ramp-and-exit on Budget/RBI/earnings; **hold-through only on elections/binary surprises.**
- **Sizing:** quarter-Kelly on the *conditional* edge; bankroll = net worth; four frozen drawdown numbers; pure long premium → no forced square-off.
- **Costs:** custom FeeModel (STT 0.10/0.15%, txn, GST, stamp); never let ITM expire; minimize rolls.
- **Eval:** 4-metric panel (Sharpe/Sortino/Calmar/rolling-Sharpe±Lo-SE), per-regime; expect ugly short-window Sharpe.
- **Build order:** (1) §F POC gate → (2) RV/IV signal + cone in LEAN → (3) custom FeeModel → (4) straddle entry + WW band hedge → (5) event calendar overlay → (6) sizing & metrics → (7) the regime log.

---

## I. Caveats & verification ledger

- **STT changes Apr-1-2026 → 0.15%/0.15%** — update the fee model if a backtest crosses FY26-27. (Note: STT did **not** triple in 2024 — it rose ~60%, 0.0625%→0.10%, on Oct-1-2024.)
- **SEBI 2024-25 reforms invalidate pre-2024 backtests** (lot sizes, weekly cuts, Tuesday expiry, expiry-day ELM). Don't trust historical NSE edge estimates across that break.
- The India VRP figure (~+1.2 vol pts, ~75% of days) comes from one strong recent study (Aug-2022→Mar-2026, 43M one-minute bars); directionally solid, not gospel. Indian regime history is short — peso-problem and small-sample caveats are acute.
- **Verification coverage:** 34/34 candidates + 6 fact dossiers. 20 candidates via full 3-lens panels, 4 via two-lens panels, 10 via consolidated panels. Outcome: every tier placement confirmed; the only upgrades were the §F POC gate and the §G regime log elevated to Tier 1. Raw evidence: `verified.json`, `verified_compact.json`, `topup_rulings.json`.

---

## Appendix — full 34-candidate verdict ledger

Verdict legend: **T1** = adopt as core, **T2** = conditional/secondary, **✗** = does-not-work / informational for NSE retail.

### Tier 1 — the core program
| Candidate | Category | Verified ruling |
|---|---|---|
| forecast-vs-implied-core | forecasting-timing | holds w/ caveats — gate on forecast RV > IV **+ VRP**, with a catalyst |
| variance-premium-is-the-headwind | VRP-aware entry | holds w/ caveats — passive long vol bleeds; premium is regime-conditional |
| gamma-scalp-pnl-engine | gamma-hedging | holds w/ caveats — engine correct; carry invariant is per *vega* |
| hedge-band-not-continuous | gamma-hedging | holds w/ caveats — WW band, widen for long gamma |
| hedge-at-expected-vol | gamma-hedging | holds w/ caveats — Hull-White MV delta; reduces leakage not edge |
| futures-hedge-index | gamma-hedging | holds w/ caveats — NIFTY future hedge; carry still in basis |
| path-dependence-noise | pnl-distribution | holds w/ caveats — size to noise; need positive edge first |
| structure-straddle-strangle | structure-selection | holds — NIFTY straddle/strangle; the retail long-vol vehicle |
| strike-itm-vs-otm | instrument-selection | holds — near-ATM core; far-OTM only hold-to-payoff |
| kelly-fractional-sizing | sizing-risk | holds w/ caveats — quarter-Kelly on *conditional* edge; mind sign |
| drawdown-budget-pre-commit | sizing-risk | **core-keep** — four frozen numbers; survive 8–12wk calm |
| no-pnl-stop-thesis-exit | psychology | holds w/ caveats — pure long premium only; exit on thesis |
| short-term-thinking-bias | psychology | holds w/ caveats — 5-line pretrade card; judge process |
| performance-metrics-sortino | pnl-distribution | holds — Sharpe+Sortino+Calmar+Lo-SE panel |
| instrument-selection-liquid-niche | instrument-selection | holds w/ caveats — NIFTY core, passive limits; names hold-to-payoff |
| cost-management-roll-and-execution | cost-management | **holds** — custom FeeModel mandatory; never expire ITM |
| semi-static-jump-hedging | earnings-jumps | holds w/ caveats — own jumps statically; flatten before prints |
| event-iv-crush-trap | earnings-jumps | holds — trade the ramp, exit before; hold-through only fat-tails |
| vol-cone-percentile-entry | forecasting-timing | holds w/ caveats — necessary-not-sufficient cheapness gate |
| regime-200dma-and-vix-filter | regime | holds w/ caveats — state veto, not a mechanical buy trigger |
| qc-execution-data-reality | instrument-selection | recast — run the 1-expiry POC gate first; else pivot to US |
| edge-dissipation-monitoring | regime | holds w/ caveats — keep the weekly regime log |

### Tier 2 — conditional / secondary
| Candidate | Category | Verified ruling |
|---|---|---|
| earnings-jump-decomposition | earnings-jumps | holds w/ caveats — index-adjust then test front-vs-forward; single-name friction-heavy |
| long-skew-is-expensive-insurance | skew | holds w/ caveats — paid crash insurance, sized small |
| tail-hedge-budget-and-timing | tail-hedging | holds w/ caveats — election-timed convexity, ~1–2%/yr cap |
| vol-pair-rv-trade | correlation-dispersion | holds w/ caveats — NIFTY/BANKNIFTY weak; monthly-vs-monthly only |
| var-swap-replication-strip | structure-selection | recast — put-heavy truncated NIFTY strip, fat-tail windows only |
| realized-vol-estimator-toolkit | forecasting-timing | keep w/ conditions — one Yang-Zhang series + ±25% band |
| volume-and-fundamental-confirmation | forecasting-timing | keep w/ conditions — confirmation overlays, never triggers |

### ✗ Does-not-work / informational for NSE retail
| Candidate | Category | Verified ruling |
|---|---|---|
| dispersion-long-singlestock-vs-index | correlation-dispersion | **refuted** for NSE retail — recast to US |
| analyst-dispersion-screen | earnings-jumps | **refuted** for NSE — concentrates you in the most overpriced events |
| calendar-term-structure | term-structure | **refuted** as long-vol — short-gamma sign error |
| avoid-vix-products-no-india-vix | vix-products | recast — no tradable India VIX; guardrail + gauge only |
| leveraged-product-vol-drag | instrument-selection | recast (T3) — no Indian product; sign-screening discipline only |

---

*Artifacts in this folder: `text/sinclair.txt`, `text/trading_vol.txt` (full source extracts); `cands.json` (34 candidates with citations); `verified.json` / `verified_compact.json` (lens verdicts + fact dossiers); `topup_rulings.json` (10 consolidated rulings).*
