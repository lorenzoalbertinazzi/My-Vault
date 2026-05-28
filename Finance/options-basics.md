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

### Calendar Spreads (Time Spreads)

A **calendar spread** (also: horizontal spread, time spread) involves buying and selling options on the same underlying and same strike, but with different expirations:

**Long calendar spread**: Buy a longer-dated option, sell a shorter-dated option at the same strike.
- **Motivation**: Exploit the differential in time decay rates. The short near-term option loses time value faster than the long far-term option (theta is highest near expiration). If the stock remains near the strike through the near-term expiration, the short option expires worthless and the long option retains most of its value — effectively reducing the cost of the long option.
- **Maximum profit**: Achieved when the stock is exactly at the strike at near-term expiration — the short option has maximum theta decay while the long option retains value.
- **Risk**: If the stock moves significantly in either direction before near-term expiry, both options go OTM and the spread loses.

**Earnings calendar spreads**: Sell the front-month option (inflated IV before earnings = expensive premium) while buying the next-month option (lower IV). Profit from IV crush on the front-month option when it expires. The long option retains exposure to the post-earnings story.

**Diagonal spread**: Combines different strike AND different expiration — the most flexible spread structure. A common implementation: buy a deep ITM LEAPS call and sell a near-term OTM call repeatedly against it (synthetic covered call).

### Employee Stock Options (ESOs) in Corporate Compensation

Employee stock options differ structurally from exchange-traded options in important ways:

**Key ESO features**:
- **Strike price** = stock price on grant date
- **Vesting schedule**: Typically 4-year vesting with a 1-year cliff (25% after year 1, then monthly/quarterly)
- **Expiration**: Typically 10 years from grant (exchange options max ~3 years)
- **Non-transferable**: Cannot be sold; the only monetization path is exercise
- **Exercise price is fixed**: ESOs have positive value only if the stock rises above the strike

**US tax treatment**:
- **Non-Qualified Stock Options (NSOs)**: Spread between exercise price and market price at exercise is ordinary income; company gets a corresponding deduction
- **Incentive Stock Options (ISOs)**: No ordinary income tax at exercise (only capital gains tax at eventual sale), but spread at exercise counts toward Alternative Minimum Tax. ISO treatment caps at $100K/year in options becoming exercisable.

**Valuation nuances**: ESOs are worth *less* than equivalent exchange-traded options because (a) they cannot be sold or hedged; (b) employees are undiversified and bear concentrated company risk; (c) early exercise is common due to liquidity needs, destroying time value. Companies use Black-Scholes or binomial models for financial statement reporting (ASC 718), typically discounting for expected early exercise.

**Behavioral note**: ESOs create strong anchoring to the grant-date strike price. Employees irrationally attach to options near their strike and are reluctant to sell shares acquired through exercise even when diversification would be optimal — a classic endowment effect plus anchoring combination.

## Cross-Disciplinary Connections

### Options as the Mathematics of Uncertainty Priced

Options are unique among financial instruments in that their value is primarily a function not of the direction of price movement, but of the degree of uncertainty about future prices. This makes options markets the purest distillation of collective human judgment about risk, probability, and the future — and consequently the richest intersection of finance, psychology, and information theory. The implied volatility surface is, in a real sense, the market's probability distribution over future outcomes made visible and tradeable. Understanding options deeply is inseparable from understanding how uncertainty is psychologically perceived, systematically mispriced, and sometimes violently corrected.

The connection to [[behavioral-finance-and-investor-psychology]] runs through every dimension of options pricing. The volatility skew — where OTM puts trade at systematically higher implied volatility than OTM calls for equity indices — is the mathematical signature of loss aversion in aggregate. Prospect Theory predicts that losses feel roughly 2.25× more painful than equivalent gains; the options market prices this asymmetry directly into the shape of the implied volatility surface, with downside tail scenarios priced at a persistent premium to upside tails. This "volatility risk premium" — the spread between implied volatility and subsequently realized volatility — is consistently positive for equity index puts: the market systematically overpays for downside insurance relative to its actuarial value. The reason is the very real behavioral cost that a portfolio drawdown imposes, which is not captured by mean-variance optimization but is fully internalized by institutional investors managing to drawdown-sensitive mandates. Tail hedging using options is therefore not an economically irrational activity even if the expected dollar return on the hedge is negative — it is paying to eliminate the left-tail outcome that, for a leveraged institution or a retiree in the withdrawal phase, could be existential.

### Options, Portfolio Theory, and Distributional Engineering

The relationship between options and [[portfolio-theory]] is one of the most practically important cross-connections in modern finance. Standard portfolio theory (Markowitz) assumes returns are normally distributed and risk is fully captured by variance. Both assumptions are violated in practice: equity returns have fat left tails (crashes are more frequent and severe than normal distributions predict) and skewness (large down moves occur more often and more violently than large up moves). Options provide the tools to engineer non-normal return distributions deliberately. A covered call strategy converts an equity position from unlimited upside/downside to a capped upside/retained downside distribution — appropriate for income-seeking investors who are willing to sacrifice upside to reduce premium cost. A collar (protective put + covered call) creates a bounded distribution with defined floor and ceiling — appropriate for concentrated positions approaching liquidity events. A risk-reversal (sell OTM put, buy OTM call) expresses a bullish conviction with specific risk/reward engineering that no combination of the underlying and cash can replicate.

The Sharpe ratio — the standard portfolio theory metric for risk-adjusted performance — is deeply misleading when applied to options-heavy strategies. Because Sharpe penalizes all volatility equally (including upside volatility), strategies that systematically sell volatility (like covered calls or systematic put-writing) appear to have outstanding Sharpe ratios during calm markets. But these strategies have engineered a return distribution with positive skewness replaced by strong negative skewness (large tail losses in crashes) — the Sharpe ratio cannot detect this. The Sortino ratio (penalizing only downside volatility) and measures like CVaR or maximum drawdown better capture the risk of these strategies. This is why the practical risk management framework documented in the portfolio theory note benefits enormously from incorporating options-literacy: an investor who understands gamma and theta can diagnose the hidden risk profile of any strategy in ways that mean-variance statistics cannot.

### Technical Analysis and the Options Flow Signal

The intersection of options markets with [[technical-analysis-and-chart-patterns]] operates through a mechanism that has grown dramatically more important in recent years: dealer gamma positioning. When market makers sell options to clients (which they typically do, being net short options), they are short gamma — meaning they must buy the underlying as it rises and sell as it falls to maintain delta-neutral hedges. This mechanical buying and selling creates patterns in price action that technical analysts can observe but may misinterpret as fundamental-driven trends. When dealers are deeply short gamma (as they often are near options expiration dates), every price move is amplified because hedging activity reinforces the direction. Conversely, when dealers are long gamma (net buyers of options from clients selling covered calls, for instance), their hedging activity stabilizes prices — creating the pinning behavior around major strike prices that technical analysts observe as magnetic support/resistance at round numbers.

The VIX's behavior at technical inflection points is itself a behavioral indicator: spikes in the VIX during index sell-offs often overshoot because institutional demand for protection surges precisely when protection is most expensive — a manifestation of the availability heuristic operating in the options market. Technical traders who recognize that VIX spikes above 40 have historically been excellent buying signals are implicitly exploiting behavioral overshoot in the implied volatility market. The Bollinger Band "squeeze" in the underlying index frequently correlates with VIX term structure entering backwardation — both are telling the same story (unusual uncertainty concentration) from different data streams.

### Geopolitical Risk and the Options Market's Reaction Function

Options markets are the most immediate financial channel through which geopolitical risk is priced. When the [[2026-05-28-usa-iran-conflict-2026-war-and-nuclear-negotiations]] escalated, the first market reaction was not in equity prices but in the options market: implied volatility on oil futures options spiked dramatically before spot oil prices fully adjusted, as market participants rushed to buy call protection on oil and put protection on airline, shipping, and energy-intensive industrial equities. This front-running of fundamental repricing by the options market occurs because options allow investors to take asymmetric positions on uncertain outcomes — they are structurally designed for geopolitical uncertainty pricing. The "geopolitical risk premium" embedded in options during crises — the spread between implied volatility and any reasonable fundamental volatility estimate — is a real-time measure of how seriously the options market is treating tail scenarios. Analysts who track the spread between near-dated and far-dated options (volatility term structure) during geopolitical crises can extract probability-weighted market assessments of scenario resolution timelines.

## Related
- [[valuation-fundamentals]]
- [[portfolio-theory]]
- [[behavioral-finance-and-investor-psychology]]
- [[technical-analysis-and-chart-patterns]]
- [[macroeconomics-101]]
- [[cognitive-biases]]
- [[2026-05-28-usa-iran-conflict-2026-war-and-nuclear-negotiations]]
