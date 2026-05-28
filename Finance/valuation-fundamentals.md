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

### WACC Decomposition — Cost of Capital in Practice

The **Weighted Average Cost of Capital (WACC)** is the hurdle rate used to discount cash flows in a DCF, representing the blended required return of all capital providers:

> **WACC = (E/V) × Ke + (D/V) × Kd × (1 − Tax Rate)**

Where E = equity market value, D = debt market value, V = E + D.

**Cost of Equity (Ke) via CAPM**:
> **Ke = Rf + β × (Rm − Rf)**
- **Rf** (risk-free rate): Typically the 10-year Treasury yield (~4–5% in 2026)
- **β** (beta): Market sensitivity, estimated from historical regression against the market index. For private companies: "unlever" the beta of public comparables, then re-lever at the target company's capital structure
- **(Rm − Rf)** (equity risk premium, ERP): Historical premium of stocks over bonds, typically estimated at 4–6% in the US. Damodaran (NYU) publishes monthly updates

**Cost of Debt (Kd)**: Yield to maturity on existing debt, or estimated from credit rating → yield spread + risk-free rate. Tax-adjusted (×(1 − Tax Rate)) because interest payments are deductible.

**Common errors in practice**:
- Using book value weights rather than market value weights for E and D
- Applying a single WACC to all business divisions when they have different risk profiles
- Ignoring the "circularity": WACC depends on target capital structure → enterprise value → which informs the capital structure weights
- Using a short-term tax rate instead of the normalized long-run marginal rate

### The Dividend Discount Model (DDM)

The **DDM** values a stock as the present value of all future dividend payments — the purest expression of the intrinsic value framework for dividend-paying companies:

> **Gordon Growth Model (constant growth): P = D1 / (Ke − g)**

Where D1 = next year's expected dividend, Ke = cost of equity, g = perpetual growth rate.

**The denominator sensitivity**: When Ke − g is small, the multiplier explodes. If Ke = 10% and g = 8%, the fair value P/E is 50×. If g = 7%, it's 33×. A 1% change in the perpetual growth assumption changes value by ~50% — making the terminal assumption the dominant uncertainty.

**Multi-stage DDM** (more realistic):
1. **High-growth phase** (years 1–5): Project specific dividend amounts from earnings growth and payout ratio
2. **Transition phase** (years 6–10): Growth rate gradually converges toward the terminal rate
3. **Terminal phase**: Apply Gordon Growth Model with a stable, conservative long-run g (≤ nominal GDP growth)

**When DDM is most useful**:
- Mature companies with stable, predictable dividends (utilities, consumer staples, regulated infrastructure)
- Financial companies (banks, insurers) where free cash flow is difficult to define but dividends are well-defined regulatory distributions of excess capital
- Any business where shareholder returns are primarily delivered via dividends rather than buybacks or reinvestment

**DDM limitations**: Useless for non-dividend-paying companies (Berkshire Hathaway, Amazon). Can be extended to the **dividend + buyback** framework — treating buybacks as equivalent to dividends — which is the correct generalization. DDM and DCF yield identical values when consistently applied; they are different ways of expressing the same present value mathematics.

## Cross-Disciplinary Connections

### Valuation as Behavioral Finance in Disguise

Every valuation model contains hidden psychological assumptions — and recognizing them transforms valuation from a mechanical exercise into a discipline of managing cognitive biases. The DCF model is theoretically the purest expression of intrinsic value, but in practice it is a structured vehicle for formalizing the analyst's own beliefs and hopes. The terminal growth rate assumption — which drives 60–80% of DCF value — is where confirmation bias operates most powerfully: an analyst who has fallen in love with a growth narrative will, with perfect numerical precision, choose a 3.5% terminal growth rate rather than 2.5%, and produce a value that is 30–40% higher. The reverse DCF technique partially solves this problem by inverting the question: rather than asking "what is this company worth?" (which invites anchoring to a desired conclusion), it asks "what does the current price require you to believe?" This framing leverages the psychological anchoring concept from [[behavioral-finance-and-investor-psychology]] in the opposite direction — anchoring not to a prior price but to current market prices, forcing the analyst to explicitly engage with the embedded expectations before overriding them with private forecasts.

The relative valuation approach (comparable company analysis, precedent transactions) is even more psychologically loaded than DCF because it imports market-wide behavioral dynamics directly into the analysis. When the market is in a bubble, comparable company multiples are inflated by the same herding and narrative contagion that drives the bubble itself. An analyst applying a 25× EV/EBITDA multiple derived from bubble-era peers is not doing objective valuation — they are formalizing collective irrationality in a spreadsheet and presenting it as analysis. This is the "conventional" valuation mode that Keynes identified: not what a company is intrinsically worth, but what the average opinion thinks the average opinion will be. The margin of safety principle — Graham's most important contribution — is explicitly designed to resist this dynamic: by requiring a 30–40% discount to intrinsic value before purchase, it creates a buffer against both analytical error and the systematic overvaluation embedded in peer multiples during bubble conditions.

### Macroeconomic Rates and Valuation's Ultimate Sensitivity

The relationship between macroeconomics and valuation is mediated entirely through the discount rate — the "r" in every present value formula. When the natural rate of interest (r*) is near zero, as it was in 2015–2021, the fair P/E multiple for a mature, stable earnings stream at 2% real growth is theoretically around 40–50× — the Gordon Growth Model's denominator (r − g) becomes tiny, exploding the price. When r* is normalized and the 10-year Treasury yields 4–5% (as in 2026), the same earnings stream is worth perhaps 18–22×. This mathematical relationship between interest rates and equity multiples — essentially a duration calculation applied to an equity earnings stream — is the core of the macroeconomics-valuation link explored in [[macroeconomics-101]]. Growth stocks, with their cash flows concentrated far in the future, are extremely long-duration assets: their present value is more sensitive to discount rate changes than mature value stocks with near-term cash flows. This explains why the 2022 rate hiking cycle devastated growth multiples (high-duration assets hit hard by rising rates) while value and dividend stocks were relatively resilient (shorter-duration assets with less discount rate sensitivity).

The WACC decomposition makes this explicit: every 1% rise in the risk-free rate (Rf) raises Ke by approximately the same amount, increasing the cost of equity and reducing all equity valuations proportionally. But for a long-duration growth stock priced on 20-year earnings projections, a 1% rate rise reduces the present value of those distant cash flows by far more than for a low-growth utility paying out current earnings. The SaaS valuation collapse of 2022 — when companies trading at 30–50× revenue in 2021 fell to 5–10× revenue by 2022 — was a rate-driven repricing of long-duration assets, not a fundamental deterioration in business quality. The fixed income framework of duration applies directly to equity valuation, a cross-disciplinary insight that connects [[fixed-income-deep-dive]] and valuation fundamentals at the deepest level.

### Geopolitical Risk in the Discount Rate

Conventional WACC analysis focuses on risk-free rates and equity risk premiums derived from historical market data — a framework entirely calibrated to stable, peacetime economic conditions. Geopolitical disruptions create non-linear additions to the required discount rate that standard beta-based models cannot capture. The [[2026-05-27-us-china-great-power-competition]] context has created a structural uplift in the discount rate applied to companies with significant Chinese revenue exposure or Chinese supply chain dependencies: the probability distribution over future trade relationships now includes scenarios (export controls, tariffs, forced decoupling) that would devastate revenues without any change in fundamental business quality. Aswath Damodaran (NYU) explicitly incorporates country risk premiums into discount rates for sovereigns and multinationals — adding 2–5% to the cost of equity for companies operating in countries with elevated political risk. In the 2026 environment, this approach needs to extend to geopolitical supply chain risk even for US-domiciled companies: a semiconductor company with 40% of revenue from China faces a different risk profile than its beta alone suggests.

### Machine Learning, Alternative Data, and Valuation's Informational Revolution

The application of [[machine-learning-fundamentals]] to valuation has begun to reshape what counts as "knowable" before official financial statements are published. Alternative data — satellite imagery counting cars in retail parking lots, credit card transaction flows tracking spending by merchant category, web-scraped job postings signaling hiring intention, app store download data indicating product traction — can be processed by ML models to generate real-time estimates of company fundamentals weeks before earnings announcements. This alternative data revolution is systematically eroding the information advantage that traditional fundamental analysts previously enjoyed in the gap between quarterly earnings releases. The practical implication for valuation methodology is that models built purely on financial statement data are competitively disadvantaged against models that incorporate high-frequency alternative data signals. LLMs fine-tuned on earnings calls and management commentary can extract sentiment and guidance signals at a level of nuance that individual analysts cannot process at scale.

## Related
- [[portfolio-theory]]
- [[macroeconomics-101]]
- [[options-basics]]
- [[behavioral-finance-and-investor-psychology]]
- [[value-investing-methodology]]
- [[fixed-income-deep-dive]]
- [[machine-learning-fundamentals]]
- [[2026-05-27-us-china-great-power-competition]]
- [[cognitive-biases]]
