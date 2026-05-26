---
title: Options — Fundamentals and Strategies
date: 2026-05-26
tags: [finance, options, derivatives, trading]
source: research
last_updated: 2026-05-26
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

## Related
- [[valuation-fundamentals]]
- [[portfolio-theory]]
