---
title: Derivatives — Futures, Forwards, and Swaps
date: 2026-05-30
tags: [finance, derivatives, futures, forwards, swaps, hedging, risk-management, commodities, interest-rate-swap, CDS, cost-of-carry, basis-risk, margin, central-clearing, OTC-derivatives, SOFR, contango, backwardation]
source: "Hull (2018) Options, Futures and Other Derivatives; CME Group data; BIS Triennial Central Bank Survey (2022); McDonald (2013) Derivatives Markets; Duffie (2010) How Big Banks Fail and What to Do About It"
last_updated: 2026-05-31
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
