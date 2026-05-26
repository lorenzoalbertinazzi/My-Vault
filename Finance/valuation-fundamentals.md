---
title: Valuation Fundamentals
date: 2026-05-26
tags: [finance, valuation, investing, DCF]
source: research
last_updated: 2026-05-26
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

## Related
- [[portfolio-theory]]
- [[macroeconomics-101]]
- [[options-basics]]
