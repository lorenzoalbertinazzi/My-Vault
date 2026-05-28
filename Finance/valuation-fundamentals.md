---
title: Valuation Fundamentals
date: 2026-05-26
tags: [finance, valuation, investing, DCF]
source: research
last_updated: 2026-05-28
---

## Summary
Valuation is the process of determining the present value of an asset. There are two broad families: intrinsic value methods (what cash flows are worth) and relative value methods (what similar assets trade for). Every price you pay embeds an implicit forecast — understanding valuation forces you to make that forecast explicit.

## Key Points
- **Intrinsic value**: cash flows discounted back to present using a required rate of return
- **Relative value**: comparing multiples (P/E, EV/EBITDA) against peers or history
- **DCF** is theoretically pure but highly sensitive to assumptions
- **Multiples** are fast but can embed market-wide mispricing
- The margin of safety concept (Graham) protects against estimation error

## Details

### Discounted Cash Flow (DCF)
The DCF model says: **Value = Σ (FCFt / (1+r)^t) + Terminal Value**

- **FCF (Free Cash Flow)**: Operating cash flow minus capex. The actual cash a business generates for owners.
- **Discount rate (r)**: Usually WACC (Weighted Average Cost of Capital) for enterprise value, or cost of equity for equity value. Reflects the risk of those cash flows.
- **Terminal value**: Often 60–80% of total DCF value — the most sensitive assumption. Use Gordon Growth Model: TV = FCF × (1+g) / (r−g), where g = long-run growth rate.

**Key sensitivities**: A 1% change in terminal growth rate or discount rate can swing value by 20–30%. Always run sensitivity tables.

### Comparable Company Analysis (Comps)
Select a peer group → calculate enterprise value multiples:
- **EV/EBITDA**: Most common. Removes capital structure and depreciation differences.
- **P/E (Price/Earnings)**: Simple, widely used, but distorted by leverage and accounting choices.
- **EV/Sales**: Used for high-growth companies with no earnings.
- **P/B (Price/Book)**: Useful for financials; measures premium to net asset value.

Apply peer median (or selected multiple) to target's metric → implied value.

### Precedent Transactions
Same logic as comps, but using M&A deal multiples instead of public market multiples. Typically trade at a **control premium** (20–30% above public prices) because acquirers pay for synergies and control.

### Sum-of-the-Parts (SOTP)
Value each division separately using the most appropriate method per segment, then sum. Used for conglomerates or companies with distinct businesses (e.g., Amazon: AWS vs. e-commerce vs. advertising).

### Margin of Safety (Graham/Buffett)
Buy only when price is meaningfully below intrinsic value estimate. The gap compensates for:
- Estimation error in your model
- Unknown unknowns
- Adverse business developments

A 30% margin of safety means: if your DCF says $100, only buy at $70 or below.

### Common Mistakes
- **Garbage in, garbage out**: A DCF is only as good as its assumptions. Reverse-engineer: what does the current price imply about future growth?
- **Ignoring capital intensity**: Two companies with the same EBITDA are very different if one requires 3× the capex to sustain it.
- **Confusing enterprise value and equity value**: EV = Market Cap + Debt − Cash. To get equity value from EV, subtract net debt.

### Reverse DCF: Working Backward from the Stock Price

Instead of building a model and computing a value, the **reverse DCF** takes the current market price and asks: what growth rate must the market be implying for this price to make sense?

**Example**: A stock trades at $200 with $5 EPS and 10% cost of equity. At a 0% terminal growth rate, fair value = $5/0.10 = $50. At the current price, the market implies approximately 8–9% perpetual growth — is that realistic?

This "embedded expectations" approach (popularized by Alfred Rappaport and Michael Mauboussin) sidesteps the DCF's input-sensitivity problem: rather than producing an uncertain point estimate, it tells you what you have to believe about the future to justify today's price. Then you judge: is that belief reasonable?

### The Rule of 40 (SaaS Valuation)

For high-growth software companies, traditional P/E is meaningless (often negative). The **Rule of 40** combines growth and profitability into a single heuristic:

> **Revenue Growth Rate (%) + Profit Margin (%) ≥ 40**

A company growing 50% with −15% margin scores 35 (misses); one growing 20% with 25% margins scores 45 (passes). The rule captures the tradeoff investors implicitly make between investing for growth vs. profitability.

In practice, EV/NTM Revenue multiples for SaaS companies historically correlated well with Rule of 40 scores. As interest rates rose in 2022–2024, growth-heavy companies (low margins) re-rated sharply downward as the profitability component was re-weighted.

### LBO Valuation (Leveraged Buyout Model)

Private equity acquires companies using a high proportion of debt relative to equity. The LBO model determines the maximum price a PE firm can pay while achieving its target return (typically 20%+ IRR over 5 years):

1. **Assume an entry price and debt/equity split** (e.g., 60% debt, 40% equity)
2. **Project cash flows** to pay down debt and fund operations
3. **Assume an exit multiple** at year 5 (often same as entry multiple)
4. **Calculate IRR** on the equity portion: the profit is driven by (a) debt paydown, (b) EBITDA growth, and (c) multiple expansion/compression

**LBO value creation levers**:
- **Financial engineering**: debt paydown converts enterprise value to equity value mechanically
- **Operational improvement**: cost cuts, margin expansion
- **Multiple arbitrage**: buy low, sell high (e.g., buy a private company at 7× EBITDA, take public at 12×)

LBO model sets a **floor** for company value in M&A — a strategic acquirer must top what a PE firm can pay.

### NAV for Asset-Heavy Businesses

**Net Asset Value (NAV)** is the primary valuation framework for:
- **REITs**: Value = property portfolio at market value − debt
- **Holding companies / conglomerates**: Value = sum of stakes at current market prices − holding company overhead / debt
- **Investment management firms**: Assets Under Management × expected fee rate → present value of fee stream
- **Royalty companies**: PV of royalty streams from contracted payments

A common phenomenon: holding company **discount to NAV** (e.g., Berkshire Hathaway, Exor) — the holding company trades below the sum of its parts due to complexity discount, governance concerns, or lack of liquidity of the underlying stakes. Activists often target holding companies trading at wide NAV discounts.

## Related
- [[portfolio-theory]]
- [[macroeconomics-101]]
- [[options-basics]]
