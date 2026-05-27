---
title: Fixed Income Deep Dive — Bonds, Yield Curves, and Credit Risk
date: 2026-05-27
tags: [finance, bonds, fixed-income, yield-curve, credit-risk, interest-rates]
source: research
last_updated: 2026-05-27
---

## Summary
Fixed income encompasses debt instruments that pay investors a predetermined stream of cash flows — primarily bonds issued by governments, corporations, and municipalities. Understanding bonds requires mastering yield mathematics, duration and convexity, credit analysis, and how macro conditions move the yield curve. Fixed income markets are the largest segment of global capital markets, dwarfing equities in notional value.

## Key Points
- A bond's price and yield move inversely — when yields rise, prices fall, and vice versa
- Duration measures a bond's price sensitivity to interest rate changes (in years)
- The yield curve plots yields across maturities and is a leading economic indicator
- Credit spreads compensate investors for default risk beyond the risk-free rate
- Total return = coupon income + price appreciation/depreciation + reinvestment income
- Central bank policy rates anchor the short end of the yield curve; inflation expectations drive the long end

## Details

### Bond Basics

A bond is a loan from investor to issuer. The issuer promises to pay:
- **Coupon payments**: periodic interest (usually semi-annual) based on the face (par) value and coupon rate
- **Principal repayment**: face value returned at maturity (typically $1,000 per bond)

**Key terms:**
- **Par value / Face value**: the principal amount repaid at maturity (e.g., $1,000)
- **Coupon rate**: annual interest as a % of par (e.g., 5% = $50/year)
- **Maturity**: when the principal is repaid — short-term (<2 yr), medium-term (2–10 yr), long-term (>10 yr)
- **Yield to Maturity (YTM)**: the annualised return if held to maturity and all cash flows are reinvested at the same rate — the market's "all-in" price on a bond

### Pricing and the Price-Yield Relationship

A bond's price is the present value of all future cash flows, discounted at the prevailing market yield:

> Price = Σ [Coupon / (1+r)^t] + [Par / (1+r)^n]

Because the coupon is fixed, when market interest rates rise above the coupon rate, the bond becomes less attractive — its price falls below par ("trading at a discount"). When rates fall below the coupon, the price rises above par ("trading at a premium"). A bond priced exactly at par is "at par."

### Duration and Convexity

**Macaulay Duration**: the weighted average time until all cash flows are received, expressed in years. A bond with duration 7 will move approximately 7% in price for a 1% parallel shift in yield.

**Modified Duration**: Macaulay Duration ÷ (1 + YTM). Gives the direct % price sensitivity per 1% yield change.

> ΔPrice ≈ −Modified Duration × ΔYield × Price

**Convexity**: the curvature of the price-yield relationship. Duration is a linear approximation; convexity refines the estimate for larger yield moves. Positive convexity is desirable — the bond gains more when yields fall than it loses when yields rise by the same amount. Callable bonds can exhibit **negative convexity** near the call price.

**Key rules:**
- Longer maturity → higher duration → more rate sensitivity
- Lower coupon → higher duration (more cash flow weight at maturity)
- Zero-coupon bonds have duration equal to maturity

### The Yield Curve

The yield curve shows yields across maturities for a single issuer class (typically government bonds). Its shape contains enormous information:

| Shape | What it signals |
|---|---|
| **Normal (upward sloping)** | Investors demand a term premium for locking up capital longer; economy growing normally |
| **Flat** | Transition period; uncertainty about rates |
| **Inverted (downward sloping)** | Short rates > long rates; historically the most reliable recession predictor (10Y-2Y spread turning negative) |
| **Humped** | Rates peak in the middle of the curve |

**Theories explaining the curve:**
- **Expectations Theory**: long rates are geometric averages of expected future short rates
- **Liquidity Preference**: investors demand a premium to hold longer maturities (term premium)
- **Market Segmentation**: different investors operate in different maturity segments based on their liabilities

**Yield curve shifts:**
- **Parallel shift**: all maturities move by the same amount (most common)
- **Steepening/Flattening**: the spread between long and short rates widens or narrows
- **Twist**: short and long ends move in opposite directions

### Types of Bonds

**Government Bonds:**
- US Treasuries (T-Bills <1yr, T-Notes 2–10yr, T-Bonds 30yr) — considered risk-free for USD
- TIPS (Treasury Inflation-Protected Securities): principal adjusts with CPI
- Sovereigns of other countries carry currency and political risk

**Corporate Bonds:**
- **Investment Grade (IG)**: rated BBB-/Baa3 or above — lower yield, higher quality
- **High Yield / Junk**: rated BB+/Ba1 or below — higher yield, higher default risk

**Other types:**
- **Municipal bonds (munis)**: issued by US state/local governments; often tax-exempt
- **Agency / MBS**: backed by mortgage pools; introduce prepayment risk
- **Convertible bonds**: can be exchanged for equity at a set price

### Credit Risk and Credit Spreads

Credit spread = yield on a corporate bond minus the yield on a same-maturity Treasury. It compensates for:
1. **Default risk**: probability the issuer fails to pay
2. **Recovery rate uncertainty**: how much is recovered in bankruptcy
3. **Liquidity premium**: compensation for thinner secondary markets

**Ratings agencies** (Moody's, S&P, Fitch) assign ratings. Downgrades can force institutional sellers to unload ("fallen angels" from IG to HY).

**Key credit metrics:**
- **Interest coverage ratio** = EBIT / Interest Expense (higher = healthier)
- **Leverage ratio** = Net Debt / EBITDA (lower = less default risk)
- **Current ratio**: short-term liquidity

### Duration Risk in Practice (2022–2026 Lessons)

The 2022 rate hiking cycle was the most severe in 40 years. Long-duration bonds fell 30–40% in price as the Fed raised rates from ~0% to 5%+. The lesson: duration is "rate risk" — long bonds are not "safe" in a rising rate environment. The Silicon Valley Bank collapse (2023) was a direct casualty of mismatched duration: long-dated Treasuries funded with short-term deposits.

By 2026, with fiscal deficits running high globally, the long end of the yield curve in many developed markets reflects rising term premiums as investors demand more compensation to hold sovereign debt.

### Fixed Income as a Portfolio Tool

- **Income generation**: predictable coupon cash flows
- **Capital preservation**: shorter duration bonds reduce price volatility
- **Diversification**: historically low or negative correlation with equities during risk-off events
- **Inflation hedge**: TIPS, floating rate notes, short-duration ladders

The classic 60/40 portfolio (60% equities, 40% bonds) was stress-tested in 2022 when both fell together. Critics argue duration risk needs to be actively managed rather than assumed as automatically defensive.

## Related
- [[portfolio-theory]]
- [[macroeconomics-101]]
- [[valuation-fundamentals]]
