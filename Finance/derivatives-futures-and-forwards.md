---
title: Derivatives — Futures, Forwards, and Swaps
date: 2026-05-30
tags: [finance, derivatives, futures, forwards, swaps, hedging, risk-management, commodities, interest-rate-swap, CDS, cost-of-carry, basis-risk, margin, central-clearing, OTC-derivatives, SOFR, contango, backwardation]
source: "Hull (2018) Options, Futures and Other Derivatives; CME Group data; BIS Triennial Central Bank Survey (2022); McDonald (2013) Derivatives Markets; Duffie (2010) How Big Banks Fail and What to Do About It"
last_updated: 2026-06-09
---

## Summary
Derivatives are financial contracts whose value is derived from an underlying asset, index, rate, or event. Futures, forwards, and swaps are the three pillars of the linear derivatives market — "linear" because their payoff profiles are proportional to price movements, unlike options. The global derivatives market is the largest financial market on Earth: the Bank for International Settlements (BIS) estimates notional outstanding OTC derivatives at approximately $715 trillion as of late 2024, with exchange-traded derivatives adding hundreds of trillions more. Understanding these instruments requires grasping their mechanics, pricing theory, risk exposures, and the institutional infrastructure that makes them function.

## Key Points
- **Forwards** are bilateral OTC contracts to buy/sell an asset at a fixed price (forward price) on a future date; no money changes hands at inception
- **Futures** are standardized, exchange-traded versions of forwards with daily mark-to-market (MTM) settlement via a central clearinghouse
- **Swaps** are multi-period contracts exchanging cash flows — the interest rate swap (IRS) market alone carries ~$450 trillion in notional outstanding
- The **cost-of-carry model** prices futures: F₀ = S₀ × e^(r+u−q)T, where u = storage costs, q = convenience yield/dividends
- **Basis risk** (F − S) is the core residual risk when using futures to hedge; it approaches zero at contract expiry (convergence)
- **Variation margin** (daily cash flows) and **initial margin** (performance bond) distinguish futures from forwards operationally
- Key users: commodity producers, corporate treasurers, asset managers, speculators, arbitrageurs, and central banks

## Details

### 1. Historical Evolution

The history of derivatives is as old as commerce itself. Aristotle described Thales of Miletus (~600 BCE) using olive press options — paying small sums upfront for the right to use presses in a bumper harvest year — arguably the first documented derivative trade. Medieval forward contracts on grain appeared in 12th-century Europe, particularly at the Champagne fairs.

The modern derivatives era began with the **Dojima Rice Exchange** in Osaka, Japan (~1730), where standardized rice futures contracts with clearing mechanisms were traded — predating Western exchanges by a century. The **Chicago Board of Trade (CBOT)**, founded in 1848, established the institutional template still used today: standardized grain contracts with warehouse receipts as collateral. By 1865, CBOT introduced margin requirements and daily settlement, the defining feature of modern futures.

The **1970s were revolutionary**: the collapse of the Bretton Woods system in 1971–73 created massive FX volatility, birthing currency futures at the **Chicago Mercantile Exchange (CME)** in 1972. Interest rate futures (Treasury bond futures at CBOT, 1977) and equity index futures (S&P 500 futures at CME, 1982) followed. The OTC swaps market emerged in 1981 when the World Bank and IBM entered a landmark currency swap arranged by Salomon Brothers to exploit comparative borrowing advantages.

The **1994 Metallgesellschaft debacle** and **1998 LTCM collapse** revealed systemic risks; the **2008 financial crisis** — where uncleared CDS and other OTC derivatives amplified contagion — triggered Dodd-Frank (US, 2010) and EMIR (EU, 2012), mandating central clearing for standardized OTC derivatives and trade reporting requirements.

### 2. Forwards: Mechanics and Pricing

A **forward contract** commits two parties: the long (buyer) agrees to purchase and the short (seller) agrees to deliver a specified quantity of an underlying asset at expiry date T, at the **forward price** F₀ agreed at inception. No cash changes hands at t=0.

**Payoff at expiry:**
- Long position: S_T − F₀ (profit if spot rises above forward price)
- Short position: F₀ − S_T (profit if spot falls below forward price)

**Cost-of-Carry Pricing:**
The no-arbitrage forward price for an asset with no income and no storage costs:

```
F₀ = S₀ × e^(rT)
```

Where S₀ = current spot price, r = continuously compounded risk-free rate, T = time to maturity in years.

For assets paying dividends (dividend yield q):
```
F₀ = S₀ × e^((r−q)T)
```

For commodities with storage cost (u, as continuous yield) and convenience yield (y):
```
F₀ = S₀ × e^((r+u−y)T)
```

**Worked Example:** Crude oil spot = $75/barrel, r = 5%, storage cost = 2%, convenience yield = 1%, T = 0.5 years.
```
F₀ = $75 × e^((0.05 + 0.02 − 0.01) × 0.5) = $75 × e^(0.03) = $75 × 1.0305 = $77.29/barrel
```

**Value of an existing forward contract** at time t < T (for the long):
```
f = (F_t − F₀) × e^(−r(T−t))
```
This value can be positive or negative — a critical feature used to mark forward books to market.

**Forward Rate Agreements (FRAs):** A specialized forward locking in an interest rate on a notional principal for a future period. Used extensively by banks and corporates to hedge short-term borrowing costs. The settlement is the present value of the interest rate differential against the agreed rate.

**Counterparty Credit Risk (CVR):** The main disadvantage of forwards. If the counterparty defaults when the contract is deeply in-the-money, the gain is lost. This is the primary driver for central clearing mandates post-2008. Credit Value Adjustment (CVA) is the market's technique for quantifying this: CVA = LGD × ∫₀ᵀ EE(t) × PD(t) dt, where EE = expected exposure and PD = probability of default.

### 3. Futures: The Exchange-Traded Standard

Futures replicate forward economics but solve counterparty risk through:

1. **Standardization:** Contract size, underlying quality, delivery location, and expiry dates are fixed by the exchange. CME crude oil futures (CL) = 1,000 barrels of West Texas Intermediate, delivery at Cushing, Oklahoma.

2. **Central Counterparty (CCP) Clearing:** The clearinghouse (LCH, CME Clearing, ICE Clear) interposes itself as buyer to every seller and seller to every buyer, eliminating bilateral counterparty risk at the cost of concentrating it in the CCP.

3. **Margining:**
   - **Initial Margin (IM):** A performance bond deposited before trading. CME calculates IM using SPAN (Standard Portfolio Analysis of Risk), a scenario-based system. A gold futures contract (100 troy oz) might require $6,000–$10,000 IM on a $185,000 notional position — roughly 3–5%.
   - **Variation Margin (VM):** Daily (sometimes intraday) cash flows reflecting gains and losses. If a trader is long one S&P 500 futures contract (notional ≈ $500K) and the index falls 1%, they receive a margin call for ~$5,000 that same evening. This daily settlement eliminates the credit exposure that accumulates in forwards.

4. **Mark-to-Market (MTM):** The exchange sets a daily settlement price. P&L is realized daily, not at expiry. This has tax implications (Section 1256 in the US: 60% long-term / 40% short-term capital gains treatment for regulated futures contracts).

**Convergence:** As expiry approaches, F_T → S_T. If they diverge, arbitrageurs exploit it through cash-and-carry arbitrage (buy spot, sell futures, deliver at expiry) or reverse cash-and-carry.

**Roll Risk:** Most futures traders don't want delivery. They "roll" by closing the expiring contract and opening the next month's. The **roll cost** depends on the market structure:
- **Contango** (F > S, upward-sloping curve): Rolling costs money; the holder sells a lower-priced expiring contract and buys a higher-priced deferred contract.
- **Backwardation** (F < S, downward-sloping curve): Rolling earns money. Backwardation often signals physical market tightness (strong convenience yield).

**The Negative Oil Price Incident (April 20, 2020):** WTI crude futures (May contract) fell to −$37.63/barrel — an unprecedented event caused by storage capacity exhaustion at Cushing, Oklahoma. Traders holding long contracts days before expiry faced delivery obligations they couldn't fulfill, creating panic selling. This event illustrated that convenience yield can become deeply negative when storage is unavailable.

### 4. Key Futures Markets and Their Uses

**Commodity Futures:**
- Agricultural (CBOT): corn, soybeans, wheat, live cattle
- Energy (NYMEX/ICE): crude oil, natural gas, heating oil
- Metals (COMEX): gold, silver, copper
- Soft commodities (ICE): coffee, cocoa, sugar, cotton

A wheat farmer in Kansas might short CBOT wheat futures in June to lock in a harvest price, protecting against a price decline by October delivery. A bread manufacturer takes the opposite side, locking in raw material costs. This is **commercial hedging** — the original purpose of futures markets.

**Financial Futures:**
- **Interest rate futures** (Treasury futures, Eurodollar/SOFR futures): The CME's 10-year T-note futures are among the most liquid contracts in the world. Portfolio managers use them to adjust duration; a pension fund can add/remove years of interest rate exposure instantly without trading the underlying bonds.
- **Equity index futures** (S&P 500, Nasdaq, Nikkei, DAX): Allow rapid exposure changes. A $1 billion fund can hedge equity exposure with ~2,000 E-mini S&P 500 contracts in minutes, far faster than liquidating the portfolio.
- **Currency futures** (CME): EUR/USD, GBP/USD, JPY/USD. Corporations use these to hedge translation risk on overseas revenues.

**The Basis:** The fundamental relationship: Basis = S − F (note: some define it as F − S). In theory, basis converges to zero at expiry. **Basis risk** is the risk that this convergence doesn't happen perfectly, leaving a residual hedge error. For a Kansas wheat farmer, the basis reflects local supply/demand, transportation costs, and delivery grade differences versus the standardized CBOT contract. In financial markets, tracking error is analogous to basis risk.

### 5. Swaps: The OTC Giants

A **swap** is an agreement to exchange (swap) cash flow streams. While conceptually simple, swaps are the largest segment of the derivatives market.

**Interest Rate Swap (IRS) — Vanilla:**
Party A pays a fixed rate; Party B pays a floating rate (historically LIBOR, now SOFR post-2023 LIBOR transition) on the same notional principal. No principal is exchanged.

```
Fixed leg payment = Notional × Fixed Rate × Day Count Fraction
Floating leg payment = Notional × (SOFR + Spread) × Day Count Fraction
```

**Worked Example:** Company A has floating-rate debt ($100M, SOFR + 50bps) but wants fixed exposure. They enter a 5-year swap:
- A pays 4.5% fixed to counterparty bank
- Bank pays SOFR to A
- Net: A's effective borrowing = 4.5% + 0.5% spread = 5% fixed, regardless of rate movements

**Swap Pricing:** At inception, the swap has zero value (fair pricing). The fixed rate is set so that the NPV of fixed payments equals the NPV of expected floating payments:

```
Fixed Rate = [1 − df(T_n)] / Σ df(T_i) × δ_i
```

Where df(T_i) = discount factor at time T_i, δ_i = day count fraction for period i.

This is derived from the swap's par condition: both legs must have equal present value at inception.

**Credit Default Swap (CDS):**
The buyer pays a periodic premium (the "spread," quoted in basis points) to the protection seller. If the reference entity defaults, the seller pays par minus recovery value. The CDS spread reflects the market's view of the reference entity's default probability and loss given default.

The CDS market became infamous during the 2008 crisis. AIG had written ~$440 billion in CDS protection on mortgage-backed securities without adequate reserves. When MBS prices collapsed, AIG faced margin calls it couldn't meet, requiring a $182 billion US government bailout to prevent cascading failures across the financial system.

**Currency Swap:** Two parties exchange principal and interest payments in different currencies. Used by multinationals to access cheaper funding in foreign markets. The 1981 World Bank-IBM swap is the paradigm: IBM had Swiss franc and German mark debt it wanted to convert to dollars; the World Bank, with a AAA rating and cheap dollar borrowing, wanted exposure to those currencies.

**Total Return Swap (TRS):** One party receives the total return (capital gains + dividends/coupons) of a reference asset; the other pays a funding rate (typically SOFR + spread). Used by hedge funds to gain leveraged exposure without owning the underlying — the Archegos Capital collapse in March 2021 involved $20+ billion in TRS positions accumulated with multiple prime brokers simultaneously, none aware of the others' exposure.

### 6. The Greeks and Risk Management

For linear derivatives, the primary risks are:
- **Delta (Δ):** Sensitivity to price changes. For a futures contract, Δ ≈ 1 per unit of underlying.
- **DV01/PVBP:** For interest rate derivatives, the dollar value change per 1 basis point move. A 10-year T-note futures contract has DV01 ≈ $900.
- **Vega:** Implied volatility sensitivity (more relevant for options but affects swaption books)
- **Convexity:** Second-order interest rate sensitivity; futures have zero convexity, creating the "convexity adjustment" between futures rates and forward rates in interest rate markets

**Hedging Ratio:** How many futures contracts to use?
```
N* = (Portfolio Value × Portfolio Beta) / (Futures Price × Contract Multiplier)
```

For equity: a $50M portfolio with beta = 1.2 against S&P 500, E-mini futures price = 5,000, multiplier = $50:
```
N* = ($50M × 1.2) / (5,000 × $50) = $60M / $250,000 = 240 contracts
```

### 6a. DV01 and Interest Rate Sensitivity: Full Worked Framework

**DV01 (Dollar Value of a Basis Point)**, also called PVBP (Price Value of a Basis Point), measures the change in the dollar value of an interest rate instrument for a 1 basis point (0.01%) parallel shift in yields. It is the fundamental risk measure for all fixed income and rate derivatives.

**General DV01 formula:**
```
DV01 = (Modified Duration × Dirty Price × Notional) / 10,000
```

Or equivalently, using finite difference approximation:
```
DV01 ≈ [P(y − 0.0001) − P(y + 0.0001)] / 2
```
Where P(y) = bond price at yield y.

**Worked Example — 10-Year Interest Rate Swap DV01:**

A bank enters a 10-year IRS: pays fixed 4.50%, receives 3-month SOFR on $100M notional.

Using modified duration of the fixed leg (analogous to a 10-year bond):
- Modified Duration of fixed leg ≈ 8.2 years (for 4.5% coupon, 4.5% yield)
- Dirty price ≈ 100 (at-market swap)

```
DV01 = (8.2 × 100 × $100,000,000) / 10,000 = $82,000 per basis point
```

This means: if rates rise 1 bp, the fixed-rate payer (the bank) gains $82,000 (their fixed payment becomes relatively cheaper); if rates fall 1 bp, they lose $82,000.

**Aggregating DV01 across a portfolio:**

A rates desk with multiple positions:
| Position | DV01 | Sign (long = receive fixed) |
|----------|------|--------------------------|
| 5-yr IRS receive-fixed $200M | +$85,000 | + |
| 10-yr IRS pay-fixed $100M | −$82,000 | − |
| 10-yr T-note $50M long | +$45,000 | + |
| 10-yr T-note futures short 100 contracts | −$90,000 | − |
| **Net DV01** | **−$42,000** | Bearish (benefits from rising rates) |

The desk is net short rates by $42,000/bp. To flatten the book, they would buy ~47 10-year Treasury futures (each ~$900 DV01).

**Key Tenor Buckets for Rate Risk:**
Sophisticated risk systems decompose DV01 into "bucket" sensitivities for each point on the yield curve (1Y, 2Y, 3Y, 5Y, 7Y, 10Y, 20Y, 30Y), since curve trades require understanding which tenor is driving P&L. A steepener trade (long 2Y, short 10Y) would show a positive bucket DV01 in the 2Y bucket and negative in the 10Y bucket, even if total DV01 is zero.

**Convexity Adjustment — Futures vs. Forwards:**

A critical subtlety in interest rate markets: **Eurodollar/SOFR futures rates are NOT equal to forward rates** — they require a convexity adjustment.

Futures are marked-to-market daily; forward rate agreements (FRAs) are not. Because futures P&L is received immediately while FRA payoffs are paid at expiry, futures holders have an implicit reinvestment advantage. This advantage (a form of positive carry) depresses futures rates below equivalent forward rates.

**Convexity adjustment formula (approximate):**
```
Forward Rate ≈ Futures Rate − ½ × σ² × T₁ × T₂
```
Where σ = short-rate volatility, T₁ = time to futures expiry, T₂ = time to end of reference period.

**Example:** 5-year SOFR futures rate = 4.00%, σ = 1.0%, T₁ = 4.5 years, T₂ = 4.75 years:
```
Adjustment = ½ × (0.01)² × 4.5 × 4.75 = ½ × 0.0001 × 21.375 = 0.00107 = 10.7 bps
Forward Rate ≈ 4.00% + 0.107% = 4.107%
```

This adjustment is small for short maturities but grows quadratically with time — for 10-year futures it can reach 30–50 bps, making it economically significant for long-dated positions. Fixed income relative value funds (the LTCM-type trades) must compute this precisely.

**DV01 in Practice — Regulatory Context (Basel III FRTB):**

Under the Fundamental Review of the Trading Book (FRTB, Basel III.1, implemented 2025):
- Banks must calculate Sensitivity-Based Method (SBM) capital charges using DV01 and CS01 (credit spread DV01) across all risk buckets
- The Standardised Approach (SA) applies prescribed risk weights to sensitivity measures, replacing the internal model flexibility banks previously exploited
- Expected impact: 40–60% increase in market risk capital requirements for major rate trading banks (BIS QIS studies, 2022)

### 7. Regulation and Infrastructure

Post-2008 reforms under Dodd-Frank Title VII (US) and EMIR (EU):
- **Mandatory central clearing** for standardized swaps (IRS, CDS indices)
- **Trade reporting** to registered swap data repositories (SDRs)
- **Capital and margin requirements** for uncleared swaps

CCPs themselves became systemically important. LCH's SwapClear clears over $400 trillion in IRS annually. The concentration of risk in CCPs prompted regulators to require "recovery and resolution" plans — a CCP failure would be catastrophic, potentially triggering "fire sales" of hundreds of trillions in positions.

**Basel III/IV capital treatment:** Banks must hold capital against derivative exposures using Standardised Approach for Counterparty Credit Risk (SA-CCR), which replaced the cruder Current Exposure Method (CEM) in 2022.

### 8. Common Misconceptions and Edge Cases

**"Derivatives are purely speculative."** False. The majority of derivatives use is hedging. Airlines hedge jet fuel with oil futures; corporations hedge FX exposure with forwards; pension funds hedge interest rate risk with swaps. The BIS estimates only ~30% of OTC derivatives are between non-financial institutions.

**"Futures always converge to spot."** In extraordinary circumstances they may not — basis can widen dramatically in illiquid markets or during delivery squeezes. The Hunt Brothers' attempt to corner the silver market in 1980 caused violent basis dislocations.

**"Notional = risk."** The $715 trillion notional OTC derivatives figure is often cited sensationally. The actual replacement cost (net credit exposure) is far smaller — the BIS estimates gross market value at ~$20 trillion, and after netting agreements, net credit exposure ~$3–4 trillion. Notional is the denominator for calculating cash flows, not the amount at risk.

---

### Advanced Mechanics: The SOFR Transition and Its Market Consequences

The replacement of LIBOR (London Interbank Offered Rate) with SOFR (Secured Overnight Financing Rate) — completed in June 2023 for USD LIBOR — was the largest structural change to the global derivatives market in a generation. Understanding this transition illuminates fundamental differences in how benchmark rates work and price.

**Why LIBOR Failed**
LIBOR was a *survey-based* rate: each morning, panel banks submitted their estimated unsecured borrowing cost for various tenors (overnight to 12 months). The mechanism's flaw became apparent in 2012 when British regulators and the US Department of Justice documented systematic manipulation:
- Barclays traders instructed LIBOR submitters to move rates to benefit swap book positions
- 16 banks eventually settled LIBOR manipulation cases, paying $9 billion in combined fines (2012–2018)
- The broader structural problem: during the 2008 crisis, banks understated their actual borrowing costs (to avoid appearing distressed), causing LIBOR to *understate* actual stress — precisely when accurate stress measurement was most critical

**SOFR vs. LIBOR: Structural Differences**

| Feature | USD LIBOR | SOFR |
|---|---|---|
| Basis | Unsecured bank borrowing (estimated) | Secured (Treasury repo) (actual) |
| Type | Forward-looking term rate | Overnight (retrospective) |
| Credit component | Includes bank credit risk premium | Near risk-free (collateralized) |
| Transaction volume | ~$500M/day (sparse) | >$1 trillion/day (deep) |
| Manipulation risk | High (survey-based) | Low (transaction-based) |

**Market Impact on Derivatives**

*Swap market repricing*: Interest rate swaps that previously paid/received LIBOR required conversion to SOFR-based terms. The ISDA fallback protocol — adoption of "SOFR + ISDA spread adjustment" — created a one-time basis adjustment: for 3-month LIBOR, the ISDA spread = 26.161 bps (reflecting the historical average LIBOR-OIS spread). Any residual deviation from this fixed spread is now a basis risk that traders must manage.

*Term SOFR limitations*: Unlike LIBOR which was naturally forward-looking (3M LIBOR was the rate for a 3-month loan), SOFR is purely backward-looking. "Term SOFR" — a forward-looking compounded average published by the CME — exists but is only approved for use in loans, not in derivative contracts with end-users. This creates a gap: a corporate borrower might have a Term SOFR loan but can only hedge with SOFR swaps tied to the daily overnight rate compounded in arrears, creating a timing mismatch (day count, compounding convention differences) that generates a small but real hedge basis.

**Worked Example — SOFR Compounding in Arrears**
For a 3-month floating rate payment period (assume 91 days, starting Jan 15):
```
SOFR Compound = [(∏ (1 + SOFR_i × d_i/360)) − 1] × 360/91
```

If daily SOFR rates average 5.31% over the 91-day period with typical compounding:
- Daily rate factor ≈ (1 + 0.0531/360) applied 91 times
- Approximate compound result ≈ 5.31% × (1 + convexity adjustment)
- The convexity adjustment for 3-month SOFR vs. 3-month forward SOFR ≈ −1 to −3 bps (non-trivial for large notional)

This compounding arithmetic — which must be computed precisely from daily fixing data — is the operational complexity the financial industry absorbed during the transition, requiring reprogramming of risk systems that previously simply multiplied a single LIBOR fixing.

---

### Practical Application: Commodity Futures Hedging in Practice

**Case Study: Southwest Airlines' Fuel Hedging Program (2000–2008)**

Southwest Airlines executed one of the most successful commodity futures hedging programs in corporate history, providing a textbook illustration of futures mechanics in real-world risk management.

**Context**: Jet fuel represents ~30–40% of airline operating costs. Price volatility destroys profitability: a $1/barrel move translates to ~$40M in annual operating cost for Southwest (pre-COVID fleet).

**Southwest's program (2007 peak)**:
- Hedged ~70% of fuel consumption through 2009 using crude oil futures and options (WTI crude is the closest available liquid hedge for jet fuel; the *crack spread* between crude and jet fuel is a separate, smaller basis risk)
- Entry position: WTI crude at ~$51/barrel (2005–2006 average purchase price for 2007+ contracts)
- 2007 average oil price: $72/barrel
- 2008 peak: $147/barrel (July 2008)

**P&L from hedging**:
- Fuel cost saving: $1.5 billion in 2008 versus unhedged carriers (Southwest's hedge paid out when oil hit $147)
- Southwest reported $294M net income in 2008 — most other major US airlines posted losses
- By 2009, oil collapsed to ~$38/barrel; Southwest had locked in $51 strikes and paid above-market prices. Loss on hedges: ~$120M. Net result over 2005–2009: cumulative fuel hedging benefit of ~$3.5 billion

**Basis Risk Reality**: The hedge was in crude oil (WTI), not jet fuel directly. The jet fuel/crude spread (the *jet crack spread*) can and does move:
- Normal spread: ~$10–20/barrel
- 2008 peak spread: ~$40/barrel (jet fuel supply constraints)
- Southwest benefited less than the crude hedge alone would suggest during the 2008 peak due to widening crack spread — but still vastly outperformed unhedged peers

**Lesson**: Hedging converts price *uncertainty* into basis *risk*. The hedge was not perfect — no real-world hedge is — but a small, predictable basis risk vastly outperforms the catastrophic upside risk of an unhedged position.

---

## Cross-Disciplinary Connections

### Mathematics: Stochastic Calculus, Itô's Lemma, and No-Arbitrage Theory
Derivative pricing is fundamentally a mathematical problem solved by Itô calculus (developed by Kiyoshi Itô, 1944) for describing continuous-time random processes. Black-Scholes-Merton (1973) applied Itô's lemma to derive a partial differential equation — the heat equation, identical to that governing thermal diffusion in physics — from the no-arbitrage principle applied to a dynamically hedged portfolio. The risk-neutral pricing framework (Harrison & Kreps, 1979; Harrison & Pliska, 1981) formalized this through the First Fundamental Theorem of Asset Pricing: no-arbitrage ↔ existence of a risk-neutral probability measure Q under which all discounted asset prices are martingales. This mathematical equivalence between absence of arbitrage and existence of a pricing measure is the theoretical foundation of all modern derivatives pricing.

### Physics: Brownian Motion and the Bachelier Connection
Louis Bachelier's 1900 doctoral thesis "Théorie de la Spéculation" (supervised by Henri Poincaré) modeled random price movements in Paris Rente futures using Brownian motion — *five years before Einstein's 1905 paper* on the same mathematical object applied to pollen particles in liquid. Bachelier correctly identified that price movements follow a Markov process, that expected speculation profits are zero (the martingale property), and derived what is effectively a normal distribution for price changes. Einstein's statistical mechanics description of molecular diffusion and Bachelier's financial market description are mathematically identical processes. Diffusion equations, stochastic differential equations, and the heat equation thus connect thermodynamic physics to derivative pricing through a shared mathematical structure discovered independently in two different scientific fields.

### Law: ISDA Architecture and Derivatives Regulation
The $700+ trillion notional OTC derivatives market operates under the ISDA Master Agreement — a standardized legal contract governing netting, collateral (CSA), termination events, and dispute resolution. Close-out netting — the legal right to collapse all outstanding positions to a single net payment in a counterparty's bankruptcy — is the legal cornerstone of derivatives risk management: without it, a derivatives dealer would need to post cash for every losing position while simultaneously being an unsecured creditor for every winning position. The 2009 G20 Pittsburgh mandate (implemented via Dodd-Frank Title VII / EMIR) moved standardized OTC derivatives to central clearing — eliminating bilateral counterparty risk at the cost of concentration risk in CCPs (a systemic risk tradeoff debated in central clearing research).

### History: From Roman Grain Forwards to the SOFR Transition
Forward contracts are among the oldest financial instruments — Roman *emptio spei* (purchase of a hope, Digest 18.1.8) allowed buying expected harvest yield before the harvest: a cash-settled agricultural forward. The Dojima Rice Exchange in Osaka (1697) is the world's first organized futures exchange, with standardized rice futures enabling merchants to hedge inventory price risk. The Chicago Board of Trade (CBOT, 1848) standardized agricultural futures during the US railroad expansion era, enabling integrated national grain markets. The 2023 LIBOR cessation — migrating $400+ trillion in financial contracts from an estimated interbank rate to SOFR (Secured Overnight Financing Rate, based on actual Treasury repo transactions) — was the largest single risk management transition in history: 45 years of reference rate infrastructure rebuilt in 18 months.

### Game Theory: Futures as Price Discovery and Information Aggregation
Futures markets serve the social function of aggregating dispersed private information into a single public price — Hayek's (1945) information aggregation mechanism operationalized. Commodity futures prices predict future spot prices better than producer surveys or econometric models, because futures prices incorporate the information of all market participants simultaneously. This connects to prediction markets (Hanson's logarithmic market scoring rule) and the theory of efficient markets: prices are not just clearing mechanisms but communication devices for distributed knowledge. The strategic interaction between hedgers (who pay a risk premium for price insurance) and speculators (who provide it in expectation of capturing that premium) is the microstructure game that makes price discovery socially valuable.

## Related
- [[options-basics]] — Non-linear derivatives, puts/calls, Black-Scholes; options as complement to futures hedging
- [[fixed-income-deep-dive]] — Bond pricing, duration, convexity; interest rate swaps; repo market mechanics
- [[yield-curve-and-bonds]] — Rate environment that drives swap pricing; forward rate agreements; curve trades
- [[portfolio-theory]] — Hedging theory, risk-return optimization; futures for tactical allocation adjustment
- [[quantitative-finance-and-algorithmic-trading]] — Derivatives pricing models; stochastic calculus foundations; HFT market making
- [[behavioral-finance-and-investor-psychology]] — Speculative excess in derivatives markets; LTCM as behavioral finance case study
- [[credit-markets-and-credit-risk]] — CDS as credit derivatives; synthetic CDOs; Archegos TRS collapse
- [[currency-markets-and-fx]] — FX forwards, currency futures, cross-currency swaps; carry trade instruments
- [[real-assets-reits-and-commodities]] — Commodity futures markets; contango/backwardation dynamics; hedging by producers
- [[hedge-funds-and-alternative-strategies]] — Hedge fund strategies using swaps, futures, and forwards as primary instruments
- [[macroeconomics-101]] — Interest rate futures as monetary policy pricing; SOFR transition post-LIBOR
- [[geopolitical-risk-premium-and-markets]] — Oil futures as geopolitical risk transmission; safe-haven hedging with futures
- [[Geopolitics/2026-05-30-russia-ukraine-war-2026-frontlines-and-diplomacy]] — Commodity futures disruption from Ukraine conflict; wheat and gas derivatives
- [[World Events/2026-05-27-us-iran-conflict-strait-of-hormuz]] — Oil futures price discovery during Hormuz crisis; shipping freight derivatives

### Update — June 3, 2026: Binary Payoff Structures and Gamma in Iran Deal Positioning

**Trump "next week" deal → Brent gap-and-fill futures dynamics:** Trump's June 2 announcement that a Hormuz deal was expected "over the next week" created a sharp ~$3/barrel decline in Brent crude futures before Iran's rebuttal reversed approximately $1.50 of the move. The intraday pattern is a textbook example of binary event risk resolving through futures price discovery: deal optimism rapidly discounts the expected post-deal equilibrium; Iranian denial immediately partially reverts that discount. For commodity futures traders, the current steep backwardation structure (front-month premium to deferred months) would reverse toward contango rapidly on deal signing as the supply shortage resolves — creating a powerful roll yield reversal that amplifies the spot price impact for long futures positions.

**Oil options implied volatility: binary deal/no-deal event priced at ~$12–15/barrel in straddle premium:** The implied volatility in June and July Brent crude options — pricing approximately $12–15/barrel in straddle premium — directly reflects the binary probability-weighted magnitude of each scenario. Using Black-76 for commodity options: a $20–25/barrel downside on deal signing vs. $5–10/barrel upside on deal failure, with ~30–35% deal probability implied by the market, produces approximately $8–10/barrel fair straddle value — suggesting the market is paying slightly above theoretical fair value for convexity, consistent with elevated hedging demand during binary geopolitical uncertainty episodes. Long gamma/short delta overlays are the mechanism allowing commodity portfolios to maintain energy risk premium exposure while limiting the sharp drawdown on a deal-closure day.


### Saturday Cross-Disciplinary Synthesis: Derivatives as Distributed Risk Architecture

**Connection to Information Theory — Derivatives as Compressed Risk Information:**  
The options market implied volatility surface encodes more information about the probability distribution of future asset prices than any other financial instrument. Claude Shannon's information entropy framework (1948) can be applied to the options market: the implied volatility surface is a market-derived probability density function, with variance (ATM vol), skew (risk reversal), and kurtosis (butterfly spread) each measuring different moments of the distribution. The VIX — often called the "fear index" — is precisely an information-theoretic construct: it measures the market's aggregate uncertainty (entropy) about the S&P 500's future value, expressed as an annualized standard deviation. When VIX spikes from 15 to 45 during a crisis, information-theoretically this represents a tripling of the market's uncertainty about future outcomes — which rational portfolio theory immediately translates into a reduced optimal position size for any strategy with finite risk capacity.

**Connection to Legal and Regulatory Architecture — ISDA as Constitutional Document:**  
The ISDA Master Agreement (1992, revised 2002) is arguably the most important private law document in global finance, governing $700+ trillion in notional OTC derivatives. Its two critical legal innovations — close-out netting (collapsing bilateral exposures to a single number in bankruptcy) and the safe harbor from automatic stay in US bankruptcy (Section 560, Bankruptcy Code) — were deliberately designed to sit outside the normal creditor hierarchy. The 2012 European Banking Authority stress tests revealed that systemically important banks were running derivatives books where gross exposures were 15–20× net exposures after netting — meaning that legal netting agreements were reducing apparent systemic risk by 93–95%. The ISDA architecture is therefore not merely a contract but a systemic risk management framework that has been legislatively embedded into insolvency law across 50+ jurisdictions — an example of private financial law successfully capturing state enforcement mechanisms.

**Connection to Climate Science — Carbon Derivatives and Climate Risk Markets:**  
The most significant derivatives market development in 2025–2026 is the rapid growth of carbon derivatives markets. EU ETS (European Emissions Trading System) futures now carry notional outstanding exceeding €500 billion; US voluntary carbon market futures are growing on ICE and CME. Carbon forwards and swaps allow corporations to hedge future compliance costs (under mandatory carbon pricing) and allow financial institutions to arbitrage pricing discrepancies between regulated and voluntary carbon markets. From a climate science perspective, well-functioning carbon derivatives markets are essential for the climate transition: they provide price signals that guide capital allocation, enable risk transfer from carbon-intensive corporates to financial intermediaries willing to bear policy risk, and create liquidity that allows long-dated corporate carbon commitments (2050 net zero) to be hedged in financial markets today.

**Updated Related Connections:**  
- [[Tech & AI/cryptography-fundamentals-and-zero-knowledge-proofs]] — ZK-proofs for private derivatives settlement; privacy-preserving margin calculation  
- [[World Events/2026-05-30-uae-exits-opec-oil-market-shift]] — UAE production expansion and commodity futures market structure disruption  
- [[Psychology/game-theory-and-strategic-thinking]] — Nash equilibrium in derivative market microstructure; hedger-speculator interaction

---

### Interest Rate Swaps in the 2026 Rate Environment: Duration Management and Curve Positioning

#### The Rate Environment Shapes the IRS Market

As of June 2026, the Federal Funds Rate sits at 3.5–3.75% after a cumulative 175bps of cuts from the 5.25–5.50% peak. The 10-year US Treasury yield is approximately 4.15%, and the 2-year yields approximately 3.85% — producing a modestly positive 2s10s spread of +30bps after the prolonged inversion of 2022–2024. This yield curve configuration — a re-steepening from inversion — is the most important structural development in interest rate derivatives markets in 2026 and directly drives swap market activity.

**Why re-steepening matters for IRS markets:**
A positively sloped yield curve creates specific trading opportunities that are absent during inversions:
1. **Curve steepeners** (long 2-year, short 10-year via swaps): In the traditional "bull steepener" pattern (short rates fall faster than long rates as Fed cuts), the payer of 2-year fixed rate benefits as 2-year rates decline from ~4% toward the projected neutral rate of ~2.5–3.0%
2. **Duration extension trades**: Pension funds and insurance companies — which liability-match against long-duration liabilities — extend duration when the curve steepens, because long rates offer more compensation relative to short rates
3. **Mortgage hedging demand**: The re-steepening curve has triggered refinancing activity as mortgage rates have declined from ~7.8% (2024 peak) toward ~6.4% (June 2026). The resulting negative convexity hedging by mortgage servicers (buying 10-year Treasury futures and entering receive-fixed swaps to offset prepayment risk) has been a significant flow driver

**Worked Example: Liability-Driven Investing (LDI) Swap for a Pension Fund**

A US corporate defined benefit pension fund has:
- Liability duration: 15 years (long-dated pension obligations discounted at AA corporate rates)
- Asset portfolio: 60% equities (duration ~0.5 years), 40% bonds (duration ~7 years)
- Net portfolio duration: 0.60 × 0.5 + 0.40 × 7 = 3.1 years
- Duration gap: 15 − 3.1 = 11.9 years (underfunded duration by 11.9 years)

**To close the duration gap using receiver swaps:**
Each 10-year IRS ($100M notional, receive-fixed 4.0%) has DV01 ≈ $82,000.
Duration extension needed: 11.9 years × $500M portfolio = $5.95B in duration.
Each 10-year IRS provides approximately 8.2 years of DV01-equivalent duration per $100M notional.

Notional needed: $5.95B / (8.2/100) = $72.6M per year of duration, so for 11.9 years of duration: ~$864M notional in 10-year receiver swaps.

The fund enters $864M in 10-year receiver swaps (pays SOFR, receives 4.0% fixed). Annual premium to receive-fixed swap = ($864M × 4.0% fixed) − ($864M × SOFR).

If SOFR averages 3.5% over the period:
Annual net receipt = $864M × (4.0% − 3.5%) = $4.32M per year
Simultaneously: 1bp rate move (in fund's favor) generates: $82,000 × (864/100) = $708,480 in mark-to-market gain

This LDI application now represents the single largest systematic demand driver in long-end US swap markets — pension funds managing duration gaps of $5–10B in aggregate constitute multi-trillion-dollar demand for long-dated receive-fixed positions.

#### Carbon Derivatives: The Fastest-Growing Futures Market in 2026

The EU Emissions Trading System (EU ETS) futures market has grown to become one of the most liquid commodity derivatives markets in Europe, with notional outstanding exceeding €500 billion and daily trading volumes of €3–5 billion. ETS futures (EUA contracts: 1 contract = 1,000 tonnes CO₂ equivalent) trade on ICE Futures Europe and have price-discovery characteristics that reveal important information about climate policy trajectories.

**EUA Price Drivers (2026):**
EU ETS allowance prices have traded in the €55–72/tonne range in 2026, influenced by:
1. **Energy prices**: Higher gas prices push utilities toward coal (more CO₂-intensive), increasing demand for ETS allowances; conversely, lower gas prices reduce demand
2. **Industrial output**: The 2026 European growth slowdown (1.0–1.5% GDP growth vs. 2.5% pre-pandemic trend) has reduced industrial production and therefore industrial ETS allowance demand — putting modest downward pressure on prices
3. **Policy signal**: The European Commission's REPowerEU updates and the Carbon Border Adjustment Mechanism (CBAM) implementation create upward structural price support by signaling continued commitment to the €150/tonne long-run price target needed for industrial decarbonization
4. **Market Stability Reserve (MSR)**: The automatic withdrawal mechanism removes allowances when the total stock exceeds 833 million — currently active, providing a price floor

**Cross-market derivatives relationship: EUA futures and natural gas futures:**
The EUA/gas correlation is one of the most important cross-commodity derivatives relationships in Europe:
- When TTF (European natural gas) prices rise above ~€35/MWh (the "fuel-switching" threshold), gas-fired power generation becomes expensive relative to coal — coal usage increases — ETS demand increases — EUA prices rise
- When TTF falls below €35/MWh, gas is economic and coal is displaced — ETS demand falls — EUA prices weaken

In 2026, the Hormuz crisis has maintained elevated gas prices (TTF ~€48/MWh, up 25% from pre-crisis levels), keeping EUA demand elevated above the baseline. Energy traders hedge this cross-commodity exposure through "clean spark spread" swaps — a derivative that captures the spread between power prices, gas prices, and carbon costs simultaneously. Clean spark spread = Power price − (gas price/efficiency) − (EUA price × carbon intensity). These "clean spark spread" derivatives are among the most complex structured products in European energy markets, requiring simultaneous positions in power, gas, and carbon futures.

**Voluntary Carbon Market (VCM) derivatives: nascent but growing:**
ICE and CME both launched standardized voluntary carbon credit futures in 2022–2023, targeting the $2B+ voluntary market. These contracts face unique challenges not present in EUA futures: the heterogeneity of underlying credits (REDD+ deforestation avoidance vs. direct air capture vs. enhanced weathering) means a standardized contract requires defining a baseline credit quality, creating "basis risk" between the futures contract and the specific credits a corporation might hold or intend to retire. Despite this friction, open interest in voluntary carbon futures reached $1.2 billion by June 2026, driven by corporations seeking to hedge their 2030 net-zero commitments by locking in future credit prices before they potentially rise.

#### The SOFR Options Market: Building Term Structure From Overnight Rates

The SOFR transition created a derivatives market challenge beyond the IRS transition: the entire term structure of SOFR derivatives needed to be built from scratch using only the overnight rate. The SOFR futures curve at CME now provides the primary mechanism for building forward rate expectations, and SOFR options on these futures provide the volatility surface.

**SOFR cap/floor market mechanics (2026):**
SOFR interest rate caps — a series of caplets providing protection against SOFR rising above a strike level — are the primary rate hedge for corporate floating-rate borrowers. With $1.5T+ in US leveraged loans outstanding at SOFR + spreads, the demand for SOFR caps is structural.

**Worked example: Corporate borrower buying a SOFR cap:**
Company XYZ has a $200M term loan at SOFR + 400bps. With SOFR at 3.5%, current borrowing cost = 7.5%. The CFO wants protection against SOFR exceeding 4.5% (preventing all-in borrowing above 8.5%).

3-year SOFR cap at 4.5% strike, $200M notional:
- Cap structure: series of 12 quarterly caplets
- Each caplet pays max(0, SOFR_quarterly − 4.5%) × $200M × 0.25
- Total cap premium (market price, June 2026): approximately 150bps upfront = $3M

If SOFR averages 5.5% for one year (100bps above cap strike):
- Annual cap payout: (5.5% − 4.5%) × $200M = $2M
- Cap has paid back 67% of premium in year one alone

The cap premium (150bps) vs. the cost of the uncapped tail risk (SOFR could theoretically reach 7%+ in an inflationary shock scenario, costing $5M+ per year above the cap) makes this a straightforward insurance purchase for a heavily leveraged borrower. The 2022–2024 experience — where SOFR rose from 0% to 5.3% in 18 months, imposing enormous unhedged borrowing cost increases on leveraged companies — has significantly increased the institutional demand for SOFR cap protection, deepening the market's liquidity and improving pricing efficiency.

### Energy Derivatives in the Geopolitical Era: Brent Crude Options and the 2026 Hormuz Premium

The 2026 Iran-US conflict and the resulting Strait of Hormuz crisis provided one of the most significant stress tests of energy derivatives markets in a generation, illuminating how geopolitical risk prices through the options market and what the structure of crude oil options tells us about market-implied supply disruption risk.

**Brent Crude Options: Volatility Surface Structure**

Crude oil options trade on an implied volatility surface with three key dimensions:
- **Strike (moneyness):** ATM vs. OTM calls vs. OTM puts
- **Tenor:** Front-month vs. back-months
- **Call skew:** OTM calls carry premium relative to puts in crude oil (unlike equities where puts are expensive) — because "supply shock" scenarios (upside for oil) are more feared than demand destruction scenarios

**The Hormuz risk premium in options terms (June 2026 empirical data):**
Before the Iranian mine-laying in May 2026:
- 3-month ATM implied volatility: ~32%
- 25-delta call/put skew: +8 vols (calls premium over puts)
- 1-month ATM IV: ~28%

During the escalation (June 2026):
- 3-month ATM IV: ~58% (81% increase)
- 25-delta call skew: +22 vols (extreme call premium — market pricing upside scenarios)
- Near-term (2-week) IV: ~85% (extreme short-term uncertainty)

**Calculating the options-implied supply disruption probability:**

Using risk-neutral probability framework, the price of an OTM call tells us the probability-weighted expected value of a price spike. For a $150/barrel call on Brent (spot ~$96 at the time):

```
Call price (~$8.50/barrel) ≈ π × E[Brent - $150 | Brent > $150] × e^(-rT)

Solving backward for π (risk-neutral probability of breach):
π ≈ C / [E(payoff given exercise) × discount]
π ≈ $8.50 / [$25 × 0.995] ≈ 34%
```

This implied ~34% probability of Brent exceeding $150/barrel within 3 months — an extreme reading reflecting genuine uncertainty about Hormuz closure duration, not a permanent expectation.

**The cost-of-carry in energy futures and storage dynamics:**

The **convenience yield (q)** is unique to physical commodities and represents the value of having immediate access to the physical commodity:

```
F₀ = S₀ × e^((r + u - q)T)

For Brent crude (June 2026):
S₀ = $96/barrel
r = 5.2% (risk-free)
u = 0.8%/year storage cost (Cushing tank farms: ~$0.08/month)
q = ? (solved from futures curve)

1-month F = $97.20 → e^(0.052+0.008-q)/12 implies q ≈ 0.005/month = 0.06% convenience yield (very low, market in contango)
12-month F = $94.50 → backwardation in back-months, implying q ≈ 0.9% annual at 12-month tenor
```

**Backwardation vs. Contango:**
- **Backwardation** (front > back): F < S; convenience yield high; physical market tight; OPEC discipline
- **Contango** (front < back): F > S; storage builds; carrying costs positive; convenience yield low

The Hormuz crisis created a *transient* acute backwardation in front months (1–3 month horizon) while the back-months (6–12 months) remained in contango — encoding the market's expectation that the disruption was temporary (risk premium) rather than permanent (structural scarcity).

**Structured product application: Asian oil options for hedging**

Airlines and shipping companies hedge fuel costs using **Asian options** (average-price options) because their fuel cost exposure is the average price over the entire quarter, not a single date:

```
Asian Call payoff = max(0, Average(S₁, S₂, ... Sₙ) - K)

For Qatar Airways: hedge 70% of Q3 2026 fuel needs at $85/barrel ceiling
Cost of Asian call: $4.20/barrel (much cheaper than European call due to averaging effect)
Break-even: only achieves positive P&L if average Brent > $89.20 for Q3
```

The averaging mechanism reduces option cost by ~30–40% vs. European options on the same strike, making them the standard hedging tool in the airline industry.


---

### June 10, 2026 Addition: The SOFR Transition Aftermath and Basis Trading in Post-LIBOR Markets

**The LIBOR-to-SOFR transition: two years on.** The final cessation of USD LIBOR settings on June 30, 2023 marked the end of a 50-year era in which the London Interbank Offered Rate served as the global benchmark for hundreds of trillions in floating-rate derivatives, loans, and securities. The transition to SOFR (Secured Overnight Financing Rate) — a transaction-based rate secured by overnight US Treasury repo — fundamentally altered the basis risk landscape in interest rate derivatives.

**SOFR's structural differences from LIBOR.** LIBOR incorporated a bank credit risk component (the unsecured interbank borrowing rate during a stress period rises sharply as bank credit risk elevates). SOFR is a risk-free rate — secured against Treasuries — and therefore exhibits less volatility but also does not contain the "credit adjustment spread" (CAS) that LIBOR borrowers paid as compensation for bank default risk. The transition required the application of a **hardwired spread adjustment**: for USD LIBOR 3-month → SOFR 3-month, the CAS was fixed at **26.161 basis points** (based on the 5-year historical median difference before the transition). This spread adjustment was embedded in all ISDA-governed legacy contracts via the protocol mechanism.

**SOFR Term vs. SOFR Overnight — the new basis.** LIBOR was available in 1-week, 1-month, 3-month, 6-month, and 12-month tenors, each reflecting forward-looking rate expectations. SOFR Term (published by CME Group using fed funds futures) provides 1-month, 3-month, 6-month, and 12-month equivalents, but these are derived from derivatives markets rather than actual borrowing rates. A new basis trade has emerged: SOFR Term (used in corporate loans and some FRNs) vs. SOFR Overnight Compounded in Arrears (used in most derivatives contracts). When term market expectations diverge from realized overnight rates — particularly around Fed meeting dates — this basis creates the **SOFR basis trade**: capturing the spread between Term SOFR and realized compounded SOFR over the same period. In a standard hiking cycle, Term SOFR exceeds realized SOFR (futures anticipate more hikes than actually materialize), creating consistent positive carry for the position.

**Energy futures in the Hormuz crisis context.** The Hormuz closure (since late February 2026) has created extraordinary conditions in crude oil futures basis markets. WTI futures at ~$91/barrel (June 2026) are in sharp **backwardation** — the front-month contract trades above the 12-month contract by approximately $8/barrel — reflecting the market's view that the Hormuz disruption is temporary and oil prices will normalize. Simultaneously, Brent futures (North Sea crude, not Hormuz-dependent) trade in a much narrower backwardation, while Middle East sour crude grades (Dubai, Oman) trade at unusual premiums to their historical Brent discount, reflecting the physical supply tightness in Gulf-origin barrels. These basis relationships — WTI backwardation, the Brent-Dubai premium inversion, the WTI-WCS spread (Canadian heavy vs. US light) — are the raw material for commodity basis trades that energy hedge funds exploit during supply disruptions. A roll yield of approximately **$8/barrel × 12 months = $96/barrel annualized** on WTI futures represents an extraordinary rolling return available purely from the term structure, independent of spot price direction.

**Commodity trading advisors (CTAs) in the current environment.** CTAs — systematic trend-following hedge funds that trade primarily futures across commodities, rates, FX, and equities — have generated strong returns in 2025–2026 by being long energy futures (the Hormuz trend), long dollar (safe-haven trend), and short long-duration rates (the "higher for longer" trend). The SG CTA Index was up approximately 18% in the twelve months to May 2026 — significantly outperforming equities (+8%) and fixed income (-2%). This outperformance validates the diversifying role of systematic trend-following during geopolitical stress regimes, when persistent dislocations across multiple asset classes create the conditions that trend models are designed to exploit.

---

### Interest Rate Swap Mechanics, Duration of a Swap, and CVA/DVA

#### Interest Rate Swap: The Most Liquid Derivatives Market

An **interest rate swap (IRS)** is a bilateral agreement to exchange a series of fixed interest rate payments for floating rate payments (or vice versa) on a notional principal amount. No principal is exchanged — only interest payments are swapped. The IRS market is the world's largest derivatives market: approximately **$360 trillion** in notional outstanding (BIS OTC derivatives statistics, 2025 H1).

**Standard "vanilla" IRS mechanics:**
- **Fixed leg:** Payer A agrees to pay Fixed Rate (say, 4.10%) semiannually × Notional ($100M) = $2.05M every 6 months
- **Floating leg:** Payer B (= the fixed-rate receiver) agrees to pay SOFR compounded in arrears, quarterly, reset quarterly
- **Net settlement:** At each settlement date, the two parties net out their obligations. If SOFR-based payment = $2.10M and fixed payment = $2.05M, the fixed receiver pays $50,000 net.

**The economic logic:**
A bank that has issued 5-year fixed-rate mortgages (earning fixed) but funds itself with short-term deposits (paying floating) has a **duration mismatch** — interest rate risk. The bank enters an IRS where it *pays fixed and receives floating*: this converts its fixed mortgage income into floating income, neutralizing the duration mismatch. The mortgage customer (borrower) still pays fixed; the market (swap counterparty) absorbs the rate risk.

**Swap pricing — the par rate:**
The fixed rate on a par interest rate swap is set so that the present value of the fixed leg equals the present value of the floating leg at inception (zero initial value):
```
PV(fixed leg) = PV(floating leg)

Fixed Rate = [1 − D(T)] / Σₜ D(t_i) × (t_i − t_{i-1})
```
Where D(t) is the OIS discount factor at time t. This is bootstrapped from the OIS/SOFR curve observed in the market.

**Current par swap rates (June 2026, USD IRS):**
- 1-year: 4.35%
- 2-year: 4.05%
- 5-year: 4.28%
- 10-year: 4.52%
- 30-year: 4.85%

The "IRS curve" is inverted at the short end (2Y below 5Y) and upward-sloping at the long end — reflecting the market's expectation that the Fed will cut rates over 1–2 years but long-term rates will remain elevated due to term premium and inflation uncertainty.

#### Duration of an Interest Rate Swap

The **duration of an IRS** reflects the combined interest rate sensitivity of the fixed and floating legs:

**Duration of the fixed leg:**
Identical to a fixed-rate bond's duration with the same maturity and coupon. For a 10-year swap at 4.52% fixed, the fixed leg has Macaulay duration of approximately 8.0 years and DV01 of approximately $80,000 per $100M notional per basis point.

**Duration of the floating leg:**
The floating leg resets to market rates at each reset date. Between reset dates, the floating leg behaves like a floating rate note maturing at the next reset date — very short duration (typically days to 3 months). Floatingleg DV01 ≈ $0–$25,000 per $100M for a quarterly reset swap.

**Net swap DV01:**
```
DV01_swap = DV01_fixed − DV01_floating ≈ $80,000 − $8,000 = $72,000 per $100M

(Pay-fixed swap: positive rate sensitivity — position gains when rates fall)
(Receive-fixed swap: negative rate sensitivity — position gains when rates rise)
```

**Pension fund application:** A pension fund with $1B in liabilities (modeled as a series of fixed payments with 15-year duration = DV01 of $1.5M per bp) and a portfolio of 10-year bonds (DV01 of $800K per bp) has a duration mismatch of $700K per bp — the liabilities are more interest-rate-sensitive than the assets. A receive-fixed IRS ($700K DV01 / $72K per $100M = $970M notional in 10Y receive-fixed swaps) closes the gap, creating a liability-driven investing (LDI) hedge.

#### CVA and DVA: Counterparty Credit Risk in OTC Derivatives

**Credit Valuation Adjustment (CVA)** is the market value of the counterparty default risk embedded in an OTC derivatives position:
```
CVA = (1 − Recovery) × E[Max(MtM(t), 0)] × PD(t)
```

Where MtM(t) is the mark-to-market value at time t (exposure exists only when MtM > 0 — when counterparty *owes you* money), PD(t) is the probability of default at time t, and Recovery is the estimated recovery rate (typically 40%).

**Intuition:** You are in a 10-year pay-fixed swap with Bank X. If rates fall sharply, the swap becomes valuable to you (Bank X owes you money). But if Bank X defaults when rates are low, you receive the recovery value (40%) of what they owe — not the full amount. CVA quantifies this risk and reduces the derivative's fair value:
```
Fair Value of Derivative = Risk-Free Price − CVA (+ DVA)
```

**DVA (Debt Valuation Adjustment):** The symmetric concept — the value of *your own* credit risk to the counterparty. If you default when your derivatives position is out-of-the-money to you (you owe the counterparty), the counterparty only recovers 40% — this is a *benefit* to you, captured as DVA:
```
DVA = (1 − Recovery) × E[Max(−MtM(t), 0)] × PD_own(t)
```

**Accounting controversy:** Under IFRS 13 and ASC 820, CVA and DVA must be reflected in fair value accounting. When a bank's own creditworthiness deteriorates, DVA *increases* the reported fair value of its derivatives liabilities — a perverse income statement gain (the bank "profits" from its own credit deterioration). During the 2011 European sovereign crisis, several major banks (Citigroup, Goldman Sachs, JP Morgan) reported billions in DVA gains that partially offset trading losses — generating controversy about whether this accounting was economically meaningful.

**Post-2012 XVA framework:** The financial crisis revealed that bilateral CVA/DVA was insufficient. The full XVA framework (now standard at major dealers) includes:
- **FVA (Funding Valuation Adjustment):** Cost of funding uncollateralized derivatives positions
- **KVA (Capital Valuation Adjustment):** Regulatory capital cost embedded in long-dated derivatives
- **MVA (Margin Valuation Adjustment):** Cost of posting initial margin under UMR (Uncleared Margin Rules)
- **ColVA (Collateral Valuation Adjustment):** Cost of non-cash collateral with optionality

The sum (XVA = CVA + DVA + FVA + KVA + MVA + ColVA) explains why OTC derivative pricing has become increasingly complex and why central clearing (eliminating bilateral credit risk through a central counterparty) has become strongly preferred post-Dodd-Frank.

---

## Related

- [[credit-markets-and-credit-risk]] — CVA connects interest rate and credit risk
- [[yield-curve-and-bonds]] — IRS curve as the foundation of fixed income pricing
- [[Federal-Reserve-and-Monetary-Policy]] — SOFR as the benchmark for floating legs of swaps
- [[hedge-funds-and-alternative-strategies]] — derivatives strategies used by macro and fixed income funds

---

### CDS Mechanics, the AIG Failure Case Study, and CVA/DVA in Bank Accounting

Credit Default Swaps and the accounting treatment of counterparty risk represent the most technically complex and systemically important aspects of modern derivatives markets.

**CDS mechanics — the insurance-that-isn't:**

A Credit Default Swap (CDS) is a bilateral contract in which the *protection buyer* pays a periodic premium (the CDS spread, expressed in bps per year on notional) and receives from the *protection seller* compensation upon a *credit event* (default, bankruptcy, restructuring) by the *reference entity*. The settlement is either physical delivery (buyer delivers defaulted bonds; seller pays par) or cash settlement (seller pays par minus recovery rate). Key parameters:

- *Notional:* Face amount of reference obligation (not the premium amount)
- *Tenor:* 5-year is standard; 1-year is most liquid for high-yield
- *CDS spread:* Reflects probability of default × (1 - recovery rate). A 5-year CDS trading at 250 bps implies, under simplifying assumptions, approximately: annual default probability ≈ 250 bps / (1 - 40% recovery) ≈ 4.17% per year, or cumulative 5-year default probability ≈ 19%

**The AIG failure — the world's most expensive unhedged short put:**

AIG Financial Products (AIGFP) sold approximately $440 billion in CDS protection on "super-senior" tranches of CDO-of-ABS structures between 2003-2007 — the top 1-3% of the capital structure of diversified portfolios of AAA-rated mortgage-backed securities. AIGFP's analysis: these tranches had never experienced credit events; they were rated AAA; default correlation was low. The CDS premium received: approximately 8-12 bps per year (a very cheap premium for "risk-free" protection).

The failure: when nationwide US housing prices fell and mortgage defaults surged in 2007-2008, CDO correlation spiked from ~0.3 to ~0.9. The AAA super-senior tranches fell in market value from par to 40-60 cents, triggering *margin calls* from CDS counterparties (Goldman Sachs, Deutsche Bank, Société Générale) demanding collateral against the mark-to-market loss even without actual credit events. AIGFP had not anticipated needing to post collateral for market value changes (only for actual defaults) and had no liquidity to meet the calls. AIG's collapse required a US government bailout of $182 billion — subsequently recovered but not before AIG's counterparties were paid at par ($12.9 billion to Goldman Sachs, $11.9 billion to Société Générale), not at market-clearing prices.

The systemic lesson: CDS creates interconnectedness that amplifies rather than distributes credit risk when multiple protection sellers have identical exposures.

**CVA and DVA — accounting for counterparty risk in derivatives:**

Credit Valuation Adjustment (CVA) and Debit Valuation Adjustment (DVA) are the accounting adjustments to mark derivative positions to fair value accounting for the risk of counterparty default.

*CVA:* The present value of expected losses from counterparty default. For a bank with $100M of positive mark-to-market exposure to a counterparty trading at 300 bps CDS spread and 40% recovery rate: CVA ≈ $100M × (300 bps / 10,000) / (1 - 40%) = $100M × 0.50% = **$500,000**. This amount is subtracted from the asset value — the bank's derivative asset is worth $100M - $500K = $99.5M.

*DVA:* The bank's own credit standing creates a symmetric adjustment from the counterparty's perspective. When a bank's credit quality deteriorates, its own DVA increases — producing a positive P&L impact on its derivatives book (the bank's liabilities are worth less). This creates a perverse accounting outcome: Citigroup reported in 2007 that it had generated $1.5 billion in DVA gains from the deterioration of its own credit — its financial difficulties improved its reported earnings.

*FVA (Funding Valuation Adjustment):* The cost of funding uncollateralized derivative positions. While CVA/DVA are standard GAAP/IFRS, FVA's treatment remains contested — academic finance (Hull & White) argues FVA should not be included in fair value; banking practitioners argue it captures real economic costs. The Bank of Baroda case (2013) and Morgan Stanley's $1.5B FVA charge created a practical necessity for FVA even without consensus theory.
