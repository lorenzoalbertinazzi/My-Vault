---
title: Valuation Fundamentals
date: 2026-05-26
tags: [finance, valuation, DCF, discounted-cash-flow, WACC, EV-EBITDA, comparable-company-analysis, precedent-transactions, LBO-model, terminal-value, CAPE-ratio, margin-of-safety, reverse-DCF, NAV, DDM, country-risk-premium, Damodaran, intrinsic-value, multiples, free-cash-flow]
source: "Damodaran (2012) Investment Valuation; McKinsey Koller et al. (2020) Valuation: Measuring and Managing the Value of Companies; Graham & Dodd (1934) Security Analysis; Shiller (1998) Valuation Ratios and the Long-Run Stock Market Outlook; Rappaport & Mauboussin (2001) Expectations Investing"
last_updated: 2026-06-02
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

### Historical P/E Multiple Evolution: A Century of Valuation Data

Understanding the current multiple context requires anchoring to historical norms. The following data reveals how dramatically valuation regimes shift across economic epochs.

**S&P 500 Trailing P/E Multiples (selected years)**:

| Year | Trailing P/E | 10Y Treasury Yield | Context |
|------|-------------|-------------------|---------|
| 1929 | 21× | 3.4% | Pre-crash peak; "new era" narrative |
| 1932 | 9× | 3.7% | Depression trough; earnings collapsed |
| 1949 | 6× | 2.2% | Post-war caution; stocks deeply unloved |
| 1966 | 24× | 4.9% | "Go-go" era peak; Nifty Fifty beginnings |
| 1974 | 7× | 8.0% | Oil shock; rate surge; market bottom |
| 1982 | 7× | 14.6% | Volcker tightening; equity abandonment |
| 2000 | 44× | 6.8% | Dot-com peak; maximum valuation in history |
| 2003 | 22× | 4.0% | Post-bubble trough; earnings had reset |
| 2009 | 13× | 3.5% | Financial crisis trough (trough earnings distort) |
| 2013 | 18× | 2.9% | Post-GFC recovery; QE begins suppressing yields |
| 2021 | 38× | 1.5% | Post-COVID stimulus; near-zero rates; maximum duration |
| 2022 | 19× | 4.2% | Rate normalization repricing; growth selloff |
| 2025 | 22× | 4.5% | Normalized rates; AI growth premium embedded |

**Key observations**:
1. P/E multiples are inversely correlated with interest rates over long horizons — when rates rise, P/Es fall (higher discount rate reduces the present value of future earnings)
2. The 1974 and 1982 troughs at 7× P/E with high inflation represent the "maximum pessimism" valuation floors — markets were pricing equities as bond alternatives (earnings yield > risk-free rate), representing extraordinary long-term buying opportunities
3. The 2000 peak at 44× is the historical extreme; Robert Shiller's CAPE ratio (10-year average earnings) reached 44× in December 1999 and did not return to historical average until 2011

**The Fed Model**: A controversial but widely used practitioner framework that compares the earnings yield (1/P/E) to the 10-year Treasury yield:
- If earnings yield > Treasury yield: equities "cheap" relative to bonds
- If earnings yield < Treasury yield: equities "expensive" relative to bonds

**2025 Example**: S&P 500 earnings yield ≈ 4.5% (at 22× P/E); 10Y Treasury = 4.5% → ratio at parity. The market is not cheap relative to bonds, but not at the extreme overvaluation of 2021 (when earnings yield was ~2.6% vs. Treasury at 1.5% — the largest spread in favor of equities in modern history, which justified the bull market's continuation).

**The CAPE Ratio (Robert Shiller)**:
The **Cyclically Adjusted P/E Ratio** divides the S&P 500 price by the 10-year average of inflation-adjusted earnings — smoothing out business cycle distortions. CAPE = Price / 10-year average real earnings.

Shiller introduced CAPE in "Valuation Ratios and the Long-Run Stock Market Outlook" (1998, with Campbell), arguing that extremely high CAPE levels predicted poor subsequent 10-year returns.

**CAPE as a return predictor** (empirical evidence):
- CAPE < 10 (1982, 1920): subsequent 10-year real returns averaged ~15%/year
- CAPE 10–20 (historical norm): subsequent 10-year real returns averaged ~7%/year
- CAPE 20–30: subsequent 10-year real returns averaged ~4%/year
- CAPE > 30 (only 1929, 2000, 2020–2022): subsequent 10-year real returns averaged ~0–2%/year
- CAPE at peak 44 (December 1999): 10-year subsequent real return was approximately −1.3%/year (2000–2009 "lost decade")

**CAPE limitations**: Critics (Jeremy Siegel) argue CAPE is distorted by accounting changes post-2000 that reduced reported earnings (expensing stock options, goodwill write-offs) and by the rising share of technology companies with different capital structures. Shiller's adjusted CAPE removes these distortions but still shows elevated valuations.

---

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

### Full Worked DCF Valuation: A Realistic Example

Consider a hypothetical mid-cap software company with the following characteristics (2026 starting figures):
- Revenue: $500M, growing at 20% for 5 years, then 10% for 5 years, then 3% terminal
- EBIT margins: 15% currently, expanding to 25% by year 5, then stable
- Tax rate: 25%
- Depreciation: 5% of revenue; Capex: 7% of revenue; Working capital builds: 2% of revenue growth
- Net Debt: $200M; Shares outstanding: 50M
- WACC: 11% (5% risk-free + 6% ERP × 1.0 beta = 11%)

**Year-by-Year Free Cash Flow Projections** ($M):

| Year | Revenue | EBIT Margin | EBIT | NOPAT (75%) | D&A | Capex | ΔWC | FCF |
|------|---------|-------------|------|-------------|-----|-------|-----|-----|
| 1 | 600 | 16.5% | 99 | 74.3 | 30 | 42 | 2 | 60.3 |
| 2 | 720 | 18.0% | 130 | 97.4 | 36 | 50 | 2.4 | 81.0 |
| 3 | 864 | 19.5% | 168 | 126.4 | 43 | 60 | 2.9 | 106.5 |
| 4 | 1037 | 21.5% | 223 | 167.3 | 52 | 73 | 3.5 | 142.8 |
| 5 | 1244 | 25.0% | 311 | 233.3 | 62 | 87 | 4.1 | 204.2 |
| 6–10 | +10%/yr | 25.0% | (computed) | — | — | — | — | ~300→490 |

**Terminal Value** (end of Year 10):
FCF₁₀ ≈ $490M growing at terminal g = 3.0%  
Terminal Value = FCF₁₀ × (1+g) / (WACC − g) = 490 × 1.03 / (0.11 − 0.03) = 504.7 / 0.08 = **$6,309M**

**Present Value Calculation**:
- PV of FCFs years 1–10: Sum of each year's FCF discounted at 11% ≈ **$1,420M**
- PV of Terminal Value: 6,309 / (1.11)^10 = 6,309 / 2.839 ≈ **$2,223M**
- Enterprise Value = 1,420 + 2,223 = **$3,643M**
- Less Net Debt: −$200M
- **Equity Value = $3,443M**
- **Per Share Value = $3,443M / 50M = $68.86/share**

**Terminal value as % of total**: 2,223 / 3,643 = 61% — in the typical 60–80% range, confirming terminal assumptions dominate.

**Sensitivity Analysis** (equity value per share at different WACC and terminal growth):

| WACC \ g | 2.0% | 2.5% | 3.0% | 3.5% | 4.0% |
|----------|------|------|------|------|------|
| 9.0% | $88 | $96 | $107 | $121 | $141 |
| 10.0% | $76 | $82 | $90 | $100 | $114 |
| **11.0%** | **$66** | **$71** | **$69** | **$85** | **$95** |
| 12.0% | $57 | $61 | $66 | $72 | $80 |
| 13.0% | $50 | $53 | $57 | $62 | $68 |

The sensitivity table reveals the 2× range from most bearish (13% WACC, 2% growth = $50/share) to most bullish (9% WACC, 4% growth = $141/share). A 30% margin of safety on the central case ($68.86 × 0.70 = $48.20) would require purchasing below $48/share.

---

### Industry EV/EBITDA Multiple Reference Guide (2026 Estimates)

Relative valuation requires context. Current multiples reflect 2026 market conditions with normalized rates.

| Sector | EV/EBITDA Range | Notes |
|--------|----------------|-------|
| Technology — SaaS | 15–35× | Depends heavily on growth; profitability premium in 2024+ |
| Technology — Semiconductors | 12–25× | Cycle-dependent; NVIDIA at peak >30×; value names at 10–15× |
| Healthcare — Biotech/Pharma | 12–20× | Pipeline optionality drives wide dispersion |
| Consumer Staples | 10–16× | Defensive; resilient margins → premium |
| Energy — Integrated | 5–8× | Depressed by transition fears; ROIC-driven |
| Energy — E&P | 4–7× | Highly commodity-sensitive |
| Financial Services — Banks | P/B 0.7–1.8× | EBITDA not meaningful; use P/B and P/E |
| Real Estate (REITs) | P/FFO 12–20× | Use FFO, not EBITDA |
| Industrials — Diversified | 10–15× | |
| Utilities | 8–12× | Regulated; low growth; rate-sensitive |
| Telecom | 6–10× | High capex; mature |
| Retail — Specialty | 7–12× | E-commerce disruption pressure |
| Private Equity (buyout) | 10–13× entry | Typically sell at 15–20× if multiple expansion |

**Control premiums in M&A**: Public company comps trade at market prices; precedent transactions add a **control premium** (20–35% average, per Thomson Reuters data 2015–2024) because acquirers pay for synergies, certainty of control, and the optionality to redirect management. This is why LBO valuations (floor value) typically sit 20–30% below strategic M&A valuations (ceiling value).

---

### Damodaran's Country Risk Premium (CRP) Framework

For companies operating across multiple markets, Aswath Damodaran (NYU Stern) developed a rigorous framework to adjust the cost of equity for country-specific political, economic, and currency risk.

**The CRP Formula**:
> Ke = Rf + β × ERP_US + λ × CRP_country

Where:
- ERP_US = US equity risk premium (~5.5% in 2026 per Damodaran's January update)
- CRP_country = Additional risk premium for the specific country
- λ = Company's exposure to that country's risk (% of revenues from country / % of company revenue from all sources, scaled)

**CRP Calculation Method**:
> CRP = Country Default Spread × (σ_equity / σ_bonds)

Country default spreads are available from sovereign CDS spreads or Moody's country ratings. The scaling factor (σ_equity / σ_bonds ≈ 1.5) adjusts for the fact that equity is riskier than debt even in the same country.

**2026 CRP Examples** (approximate, Damodaran methodology):
- Germany, Australia, Canada: ~0% (negligible premium)
- Japan: ~0.5%
- South Korea, Israel: ~0.7%
- China: ~1.5–2.0%
- Brazil: ~3.0%
- India: ~2.5%
- Turkey: ~5.0–6.0%
- Argentina: ~8–12%
- Iran, Russia (sanctioned): Not investable; theoretical CRP meaningless

**Lambda (λ) calculation**: A US-listed consumer brand generating 40% of revenue from China and 60% from the US:  
λ = 0.40 (proportional to China revenue exposure)  
Additional cost of equity vs. pure domestic: λ × CRP_China = 0.40 × 1.75% = **+0.70% uplift**

This may seem small, but at a 30× P/E multiple, a 70bp increase in the discount rate reduces the fair multiple by roughly 3–4× — from 30× to 26–27×. For a $30B company, that's $3–4B of lost market cap from China exposure alone, entirely captured in the Ke adjustment.

## Cross-Disciplinary Connections

### Prospect Theory and the Psychology of Valuation Inputs

Before examining the broader behavioral dynamics of valuation, there is a specific and underappreciated intersection with [[Psychology/prospect-theory-and-decision-making]] at the level of individual analytical inputs. Kahneman and Tversky's value function — concave in gains, convex in losses, with a kink at the reference point — distorts not just sell/hold decisions but the very assumptions that go into valuation models.

Consider how terminal growth rate assumptions are set in practice. When an analyst has recently experienced or observed strong business performance (the company has grown 20% for three consecutive years), the value function's gain-domain concavity creates systematically insufficient downward adjustment. The reference point is "20% growth" and declining to 3% terminal growth feels like a loss — the asymmetric psychology causes analysts to anchor the terminal rate higher than is warranted, precisely because the "loss" of projected growth is more psychologically painful than the equivalent "gain" of getting the estimate right would be satisfying. This is the same loss aversion coefficient (λ ≈ 2.25) that drives the disposition effect, now operating on DCF model inputs rather than portfolio holdings.

The probability weighting function w(p) — where small probabilities are systematically over-weighted and moderate probabilities under-weighted — creates specific biases in scenario analysis. A DCF model that incorporates a 5% probability scenario of exceptional growth will have that scenario over-weighted by the human analyst reviewing the sensitivity table, because w(0.05) ≈ 0.12 under Prospect Theory. The rare, vivid upside scenario captures disproportionate mental attention. Conversely, a 40% probability of flat growth — the moderate probability that Prospect Theory systematically under-weights (w(0.40) ≈ 0.28) — will be unconsciously treated as less likely than it is. The practical manifestation is DCF models that are structurally optimistic: the fat tail of upside scenarios gets over-weighted, while the modal "muddling through" scenario gets under-weighted, producing price targets that systematically exceed eventual reality.

The **mental models** framework documented in [[Psychology/mental-models]] provides the corrective discipline: inverting the analysis ("What would have to be true for this company to be worth *less* than I think?"), working through base rates from comparable historical valuations, and applying the outside view (how do companies at this valuation multiple and growth rate typically perform over five years?) rather than the inside view (what specific factors make this company exceptional?). The outside view is the fundamental prescription for counteracting both loss aversion in terminal rate assumptions and probability over-weighting in scenario analysis.

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

---

### Advanced Mechanics: Sum-of-the-Parts (SOTP) Valuation

Sum-of-the-Parts valuation is the appropriate framework for conglomerates, holding companies, and multi-segment businesses where applying a single multiple to blended metrics would produce a materially distorted view of intrinsic value. Each business segment is valued using the most appropriate method for its characteristics, then segments are aggregated and corporate overhead/debt is subtracted.

**When to Use SOTP**
- Multiple segments with different risk profiles, growth rates, and capital intensity (e.g., Alphabet: advertising, cloud, autonomous vehicles, pharmaceuticals)
- Holding company discount situation (Berkshire Hathaway, IAC)
- Conglomerate with partially owned subsidiaries whose public market value is observable
- Post-spin-off "stub" value analysis

**Step-by-Step SOTP Framework**

*Step 1 — Segment identification and financial carve-out*:
Separate company financials into distinct economic segments using segment disclosures (Note: SEC regulations require disclosure of any segment >10% of revenue, assets, or profit). Allocate corporate overhead proportionally or leave it as a separate line.

*Step 2 — Segment-specific valuation methodology*:
| Segment Type | Primary Method | Secondary Check |
|---|---|---|
| Mature consumer business | EV/EBITDA comps (industry specific) | DCF |
| High-growth tech | EV/Revenue (then DCF on 5-year horizon) | Price/FCF |
| Financial services subsidiary | Price/Book or Price/Earnings | Embedded value |
| Real estate/infrastructure | Cap rate / NAV | EV/EBITDA |
| Early-stage/venture | Venture method (target value × success probability) | Comparable funding rounds |
| Publicly traded subsidiary | Market value (observable) × ownership % | — |

*Step 3 — Aggregate and subtract holding company costs*:
```
SOTP Enterprise Value = Σ (Segment EV_i) − PV(Corporate Overhead) 
SOTP Equity Value = SOTP EV − Net Debt (holding company level) − Minority Interests
```

**Worked Example — Alphabet (Google) SOTP, 2025**

| Segment | 2025E Revenue | 2025E EBITDA | Multiple | Segment EV |
|---|---|---|---|---|
| Google Search & YouTube | $245B | $108B | 18× EBITDA | $1,944B |
| Google Cloud | $50B | $10B | 12× Revenue | $600B |
| Google Services Other (Maps, Play) | $35B | $9B | 14× EBITDA | $126B |
| Waymo (autonomous vehicles) | Pre-revenue | N/A | DCF / Comparables | $40B |
| Other Bets (DeepMind, biotech) | $2B | Loss-making | 2× Revenue (speculative) | $4B |
| Subtotal Segment EV | | | | **$2,714B** |
| Less: Corporate overhead PV | | | | ($30B) |
| Less: Net debt | | | | ($30B) |
| **SOTP Equity Value** | | | | **$2,654B** |

Alphabet market cap at time of writing: ~$2,200B → implied ~17% upside if segments are valued at peer multiples.

**The Conglomerate Discount**
A persistent empirical finding: conglomerates typically trade at a 10–30% discount to the sum of their parts. Reasons:
- *Complexity premium*: Investors apply a discount for analytical difficulty and reduced transparency
- *Cross-subsidization concern*: Profitable divisions may subsidize unprofitable ones; capital allocation may not be optimal
- *Management distraction*: Diversified management may be less effective than focused competitors in each segment
- *Forced seller concern*: Investors who want exposure to one segment must accept unwanted exposure to others

The conglomerate discount creates the "sum-of-the-parts trade" opportunity: if the discount exceeds the cost of capital over the expected separation timeline, activists often push for spin-offs, divestitures, or "tracking stock" mechanisms to surface embedded value.

---

### Advanced Mechanics: Residual Income Model (RIM)

The Residual Income Model, also known as the Edwards-Bell-Ohlson (EBO) model after its academic developers, offers an alternative to DCF that connects to accounting book value and is particularly useful when cash flows are lumpy or negative but returns on equity are visible.

**Core Insight**
In a DCF, firm value depends on forecasting free cash flows indefinitely into the future. The RIM alternative:
- Start with current book value of equity
- Add the present value of future *economic profits* (return on equity above the required return)

```
V₀ = B₀ + Σ (ROEₜ − Ke) × Bₜ₋₁ / (1 + Ke)ᵗ
```

Where:
- `V₀` = intrinsic value today
- `B₀` = current book value per share
- `ROEₜ` = return on equity in year t
- `Ke` = cost of equity
- The term (ROEₜ − Ke) × Bₜ₋₁ = "residual income" = economic profit in year t

**Key Properties**:
1. If ROE perpetually equals Ke (no excess returns), V₀ = B₀ → stock should trade at book value (P/B = 1)
2. If ROE > Ke, V₀ > B₀ → stock deserves a P/B premium (high-quality businesses)
3. If ROE < Ke, V₀ < B₀ → stock should trade at discount to book (value destroyers)

This directly explains the P/B ratio: P/B > 1 implies the market believes the company will generate returns above its cost of equity; the magnitude of the premium is determined by the *duration* and *magnitude* of this excess return.

**Worked Example**:
- Book value: $25/share
- ROE forecast: 20% for 5 years, then 12% perpetuity
- Cost of equity: 10%

Year 1–5 residual income: (20% − 10%) × growing book value
- Year 1: 10% × $25 = $2.50; PV = $2.27
- Year 2: Book = $25 × 1.20 = $30; RI = 10% × $30 = $3.00; PV = $2.48
- ... (continue for 5 years; total PV ≈ $12.50)

Year 5+ perpetuity RI: (12% − 10%) × Book_5 = 2% × $62.2 = $1.24/year; PV at year 5 = $12.40; PV today = $7.70

Total intrinsic value: $25.00 + $12.50 + $7.70 = **$45.20**

*Check*: Implied P/B = $45.20/$25 = 1.81× — reasonable for a business earning 20% ROE for 5 years transitioning to 12%

**RIM vs. DCF**: Both give identical results when applied consistently. RIM is preferred when:
- Cash flows are reinvested rather than distributed (book value grows accurately)
- Financial services companies (banks, insurers) where assets/liabilities drive value more than FCF
- Early-stage companies with predictable ROE but unpredictable capex patterns

---

## Related
- [[portfolio-theory]] — Cost of capital connects DCF to portfolio theory; CAPM provides Ke for WACC
- [[macroeconomics-101]] — r* determines the discount rate foundation; macro regimes drive terminal growth assumptions
- [[options-basics]] — Equity as option on firm assets; convertible debt optionality; real options in capital budgeting
- [[behavioral-finance-and-investor-psychology]] — Confirmation bias in DCF assumptions; reverse DCF as deanchoring tool
- [[value-investing-methodology]] — Intrinsic value framework; margin of safety; ROIC compounding math
- [[fixed-income-deep-dive]] — Risk-free rate foundation; bond-equity duration analogy for growth stocks
- [[yield-curve-and-bonds]] — Term structure of discount rates; yield environment drives sector rotation
- [[credit-markets-and-credit-risk]] — Credit-adjusted discount rates; Damodaran country risk premium for sovereign spreads
- [[private-equity-and-venture-capital]] — LBO model as valuation floor; VC method for early-stage companies
- [[real-assets-reits-and-commodities]] — Cap rate valuation of REITs; NAV framework for asset-heavy businesses
- [[geopolitical-risk-premium-and-markets]] — Country risk premium framework; geopolitical uplift to discount rates
- [[hedge-funds-and-alternative-strategies]] — Merger arbitrage dependent on spread-to-intrinsic-value calculation
- [[cognitive-biases]] — Anchoring bias in multiples; garbage-in DCF from overconfident assumptions
- [[Psychology/prospect-theory-and-decision-making]] — Loss aversion in terminal growth rate assumptions; probability weighting distorting scenario analysis in DCF models
- [[Psychology/mental-models]] — Outside view and base rate analysis as corrections for inside-view optimism in intrinsic value estimation; inversion as stress-test discipline
- [[Tech & AI/machine-learning-fundamentals]] — Alternative data eroding information advantage; ML for real-time fundamental estimation
- [[Tech & AI/retrieval-augmented-generation]] — RAG systems enabling real-time retrieval of comparable transactions, filings, and analyst reports for valuation
- [[Tech & AI/docker-and-containerization]] — Containerized deployment of valuation models and alternative data pipelines in institutional research infrastructure
- [[Geopolitics/2026-05-27-us-china-great-power-competition]] — China revenue exposure uplift to discount rates; geopolitical lambda in Damodaran CRP
- [[World Events/2026-05-30-global-economic-outlook-2026-slowdown]] — 2026 valuation environment; earnings growth assumptions under slowdown
