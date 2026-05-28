---
title: Options — Fundamentals and Strategies
date: 2026-05-26
tags: [finance, options, derivatives, trading]
source: research
last_updated: 2026-05-28
---

## Summary
An option is a contract giving the buyer the right (but not the obligation) to buy or sell an asset at a specified price before a specified date. Options are used for hedging risk, generating income, or making leveraged directional bets. Understanding them requires mastering the Greeks — the sensitivity measures that govern how option prices move.

## Key Points
- A **call** gives the right to buy; a **put** gives the right to sell
- The **strike** is the agreed price; **expiry** is the deadline
- Option price = **intrinsic value** (in-the-money amount) + **time value**
- The **Greeks** quantify how price changes with market conditions
- Most option value decays as expiry approaches — this is theta decay

## Details

### The Basic Contracts

**Call Option**
- Right to BUY the underlying at the strike price
- Buyer profits when the stock rises above strike + premium paid
- Max loss (buyer): premium paid. Max gain: unlimited.

**Put Option**
- Right to SELL the underlying at the strike price
- Buyer profits when the stock falls below strike − premium paid
- Max loss (buyer): premium paid. Max gain: strike price (if stock goes to zero).

**Moneyness**
- **In-the-money (ITM)**: Has intrinsic value (call: stock > strike; put: stock < strike)
- **At-the-money (ATM)**: Stock ≈ strike
- **Out-of-the-money (OTM)**: No intrinsic value; pure time value

### The Greeks

| Greek | Measures | Intuition |
|-------|---------|-----------|
| **Delta (Δ)** | Price change per $1 move in underlying | A 0.5 delta call moves $0.50 for every $1 the stock moves |
| **Gamma (Γ)** | Rate of change of delta | High gamma = delta changes fast (most extreme near expiry, ATM) |
| **Theta (Θ)** | Time decay per day | Options lose value daily; sellers benefit, buyers are hurt |
| **Vega (ν)** | Sensitivity to implied volatility | Higher IV → more expensive options; long options benefit from rising IV |
| **Rho (ρ)** | Sensitivity to interest rates | Less important for short-dated options |

**Delta** is also roughly the probability the option expires in-the-money (a 0.30 delta option has ~30% chance of expiring ITM).

### Implied Volatility (IV)
IV is the market's expectation of future price movement, backed out from current option prices. It's not a prediction — it's a price.

- **IV Rank / IV Percentile**: How high current IV is relative to its own history. High IV rank = options are expensive (good time to sell; risky time to buy)
- **VIX**: The "fear index" — IV of S&P 500 options over the next 30 days. Spikes during market stress.

### Common Strategies

**Covered Call**: Own stock + sell a call. Generates income (premium), caps upside. Good for flat/slightly bullish view on a stock you hold.

**Cash-Secured Put**: Sell a put while holding enough cash to buy the stock if assigned. Generates income; effectively a way to buy stock at a discount if you're willing to own it.

**Long Straddle**: Buy call + buy put at the same strike. Profits from a big move in either direction. Used before known catalysts (earnings, FDA decisions).

**Credit Spread**: Sell one option, buy a cheaper one further OTM. Defined risk. Example: Bull put spread = sell 100 put, buy 95 put. Max profit = net premium received; max loss = spread width minus premium.

**Iron Condor**: Sell OTM call spread + sell OTM put spread simultaneously. Profits if stock stays range-bound. Theta-positive strategy.

### Key Concepts for Risk Management
- **Max loss on long options**: Always limited to premium paid — you can't lose more than you put in
- **Undefined risk on short naked options**: Selling naked calls has theoretically unlimited loss
- **Assignment risk**: If a short option expires ITM, you may be assigned (obligated to buy/sell the stock)
- **Early assignment**: American-style options can be exercised any time (not just at expiry)

### Theta Decay Curve
Time value doesn't decay linearly — it accelerates. An option with 30 days to expiry loses less per day than one with 7 days. This is why short-term option sellers have an edge in collecting premium quickly, while long options buyers are in a race against the clock.

### Put-Call Parity

A fundamental no-arbitrage relationship linking the prices of calls and puts with the same strike and expiry:

> **C − P = S − PV(K)**

Where C = call price, P = put price, S = current stock price, PV(K) = present value of strike (discounted at risk-free rate). If this relationship breaks down, a riskless profit is possible. In practice it holds tightly for liquid options.

**Practical use**: If you want to synthetically replicate a long call, you can buy a put, buy the stock, and borrow PV(K). Any option position can be reconstructed from its put-call parity equivalent.

### Volatility Skew and the Smile

Real options markets do not price all strikes at the same implied volatility (IV). The pattern varies by asset class:

**Equity skew** (most stocks and indices): OTM puts trade at higher IV than OTM calls. This reflects demand for downside protection ("portfolio insurance") and the empirical fact that crashes are more severe and faster than rallies. The VIX spike on a down day is typically 3–5× larger than the IV decline on an equivalent up day.

**Implied volatility smile** (commodities, FX, some indices): IV is higher for both deep OTM calls and deep OTM puts relative to ATM. This reflects "fat tails" — the market assigns higher probability than log-normal distributions predict to large moves in either direction.

**Practical implication**: When selling OTM puts for income, you receive a volatility risk premium (VRP) — you're being paid to supply insurance. This is why systematic put-selling strategies have historically been profitable but carry left-tail (crash) risk that can wipe out months of premium in one event.

### Volatility Term Structure

Just as interest rates have a yield curve, IV has a term structure. Typically:
- Short-dated IV is more volatile and often higher (near-term event uncertainty)
- Long-dated IV is calmer (mean reversion assumptions)

In normal markets, the term structure is upward sloping (contango in VIX futures). During crises, it inverts (backwardation): short-dated IV spikes massively while long-dated IV rises less — because the market expects the panic to resolve but fears it acutely right now.

### LEAPS (Long-Term Equity Anticipation Securities)

Options with expiry beyond one year (up to three years). Used for:
- **Long-term bullish bets**: Deep ITM LEAPS on quality businesses offer significant leverage with defined-risk profile
- **Reducing cost basis**: Sell covered calls against LEAPS position to collect premium over time
- **Buffett's use**: Berkshire has sold long-dated puts on equity indices — receiving premium upfront, with the view that over multi-year periods indices almost certainly recover from temporary declines

### Gamma Scalping

A delta-neutral trading strategy that profits from realized volatility exceeding implied volatility:
1. Buy an option (long gamma)
2. Delta-hedge by selling/buying the underlying to stay neutral
3. Each time the stock moves, delta-hedge back to neutral — this generates cash from the moves
4. If realized vol > IV paid for the option, the hedging profits exceed theta decay → net positive P&L

Market makers who buy options systematically gamma-scalp to monetize their position. The strategy's profitability depends on the spread between realized and implied volatility.

### Tail Hedging and VIX Products

**Tail hedges** are options positions designed to profit massively in market crashes, offsetting portfolio losses:
- Long OTM put spreads on SPY
- Long VIX call spreads (profit when the VIX spikes)
- Long UVXY (2× leveraged VIX ETP) — but severe decay in contango regimes

**VIX ETP decay problem**: Because VIX futures are usually in contango (short-dated futures are cheaper than longer-dated), rolling VIX ETPs from front-month to next-month futures creates a systematic drag. UVXY loses ~50–80% of its value per year in calm markets even as a hedge against spikes. Sophisticated tail hedgers prefer options on VIX rather than ETPs.

**Universa/Taleb approach**: A small allocation (e.g., 3.3%) to tail hedges that cost ~0% in expected value but pay 100%+ in crashes can provide asymmetric portfolio protection — the "convex position" in a barbell.

## Related
- [[valuation-fundamentals]]
- [[portfolio-theory]]
