---
title: Discounted Cash Flow Analysis
date: 2026-06-10
tags: [finance/valuation, finance/corporate-finance, finance/quantitative, finance/equity-research, finance/capital-budgeting]
source: "Damodaran, A. (2012). *Investment Valuation: Tools and Techniques for Determining the Value of Any Asset* (3rd ed.). Wiley. Brealey, R., Myers, S. & Allen, F. (2023). *Principles of Corporate Finance* (14th ed.). McGraw-Hill."
last_updated: 2026-06-10
---

## Summary

Discounted Cash Flow (DCF) analysis is the foundational intrinsic valuation method in corporate finance. It states that an asset's value equals the present value of all its expected future cash flows, discounted at a rate that reflects their riskiness. Applied to businesses, DCF values equity as the present value of free cash flows to the firm (FCFF) or free cash flows to equity (FCFE), discounted at WACC or the cost of equity respectively. Despite its theoretical elegance, DCF is hypersensitive to assumptions about growth rates, margins, and discount rates — a feature that makes it both a powerful analytical framework and a tool easily manipulated to reach predetermined conclusions.

---

## Key Points

- **Core identity**: Value = Σ [FCFₜ / (1+r)ᵗ] + Terminal Value / (1+r)ᴺ
- Terminal value typically represents **60–80% of total DCF value** — making long-run growth assumptions the single most impactful input
- WACC = [E/(E+D)] × Kₑ + [D/(E+D)] × Kd × (1−t) — blends cost of equity and after-tax cost of debt
- CAPM is the standard Kₑ model: Kₑ = Rₓ + β × ERP
- The Gordon Growth Model (perpetuity formula) is used for terminal value: TV = FCFₙ₊₁ / (WACC − g)
- Sensitivity tables ("tornado charts") are essential — small changes in g and WACC swing value dramatically

---

## Details

### Free Cash Flow: Definition and Calculation

**Free Cash Flow to the Firm (FCFF)** is the cash available to all capital providers after operating expenses, taxes, and reinvestment:

```
FCFF = EBIT × (1 − tax rate) + Depreciation & Amortization
       − Capital Expenditures − ΔNet Working Capital
```

**Free Cash Flow to Equity (FCFE)** is what remains for equity holders after debt service:

```
FCFE = Net Income + D&A − CapEx − ΔNWC + Net Borrowing
```

The distinction matters for which discount rate to use:
- Discount **FCFF** at **WACC** → Enterprise Value; subtract net debt → Equity Value
- Discount **FCFE** at **Cost of Equity** → Equity Value directly

### Calculating WACC Step by Step

WACC = (E/V) × Kₑ + (D/V) × Kd × (1 − t)

**Step 1 — Cost of Equity (Kₑ) via CAPM:**
```
Kₑ = Rₓ + β × ERP + size premium (if applicable)
```
In June 2026:
- Risk-free rate (Rₓ) = 10-year US Treasury yield ≈ 4.4%
- Equity Risk Premium (ERP) — Damodaran's implied ERP estimate for US market as of January 2026 ≈ 4.2%
- Beta (β) varies by company; see worked example below

**Step 2 — Cost of Debt (Kd):**
Use the yield to maturity on outstanding debt (or a synthetic rating-based spread). After-tax: Kd × (1 − marginal tax rate).

**Step 3 — Capital Structure Weights:**
Use **market-value** weights (not book values). For a company with $10B market cap and $2B net debt: E/V = 10/12 = 83.3%; D/V = 2/12 = 16.7%.

### Worked Numerical Example — Microsoft Corporation (MSFT), June 2026

**Inputs:**
- Current stock price: ~$430; shares outstanding: ~7.45B → Market cap ≈ $3,204B
- Net debt: approximately $−80B (net cash position, so enterprise value < market cap)
- Trailing twelve-month revenue: ~$261B; EBIT margin ≈ 44%; effective tax rate ≈ 19%
- D&A: ~$13B; CapEx: ~$44B (elevated by AI infrastructure investment); ΔNWC ≈ $2B
- Beta (MSFT vs. S&P 500, 5-year monthly): ≈ 0.88

**Step 1 — FCFF for Year 0:**
```
FCFF = 261B × 0.44 × (1 − 0.19) + 13B − 44B − 2B
     = 114.84B × 0.81 + 13B − 44B − 2B
     = 93.0B + 13B − 44B − 2B
     = $60.0B
```

**Step 2 — WACC:**
```
Kₑ = 4.4% + 0.88 × 4.2% = 4.4% + 3.70% = 8.10%
Kd (after-tax) = (MSFT net cash position → ignore; treat D=0)
WACC ≈ 8.10%
```

**Step 3 — 5-Year Projection (explicit period):**
Assume FCFF grows at 12% in years 1–3 (Azure + AI Copilot expansion), then decelerates to 8% in years 4–5:

| Year | Growth | FCFF ($B) | PV Factor (8.10%) | PV ($B) |
|------|--------|-----------|-------------------|---------|
| 1 | 12% | 67.2 | 0.926 | 62.2 |
| 2 | 12% | 75.3 | 0.857 | 64.5 |
| 3 | 12% | 84.3 | 0.793 | 66.9 |
| 4 | 8% | 91.1 | 0.734 | 66.9 |
| 5 | 8% | 98.3 | 0.679 | 66.7 |
| **Sum** | | | | **327.2** |

**Step 4 — Terminal Value (Gordon Growth, g = 3.5%):**
```
FCF₆ = 98.3B × 1.035 = 101.7B
TV₅ = 101.7B / (0.081 − 0.035) = 101.7B / 0.046 = $2,211B
PV(TV) = 2,211B × 0.679 = $1,501B
```

**Step 5 — Enterprise Value and Equity Value:**
```
EV = 327.2 + 1,501 = $1,828B
Equity Value = EV − Net Debt = 1,828 − (−80) = $1,908B
Intrinsic Value per Share = 1,908B / 7.45B = $256
```

At $430/share, the model implies MSFT trades at a ~68% premium to this base-case DCF — reflecting either (a) the market ascribing much higher AI-driven growth rates (perhaps 15–18% in years 1–3), (b) a lower WACC, or (c) genuine overvaluation. This illustrates the "garbage in, garbage out" risk.

### Sensitivity Table — Equity Value per Share ($ ) vs. Terminal Growth Rate and WACC

|  | g = 2.5% | g = 3.0% | g = 3.5% | g = 4.0% | g = 4.5% |
|--|----------|----------|----------|----------|----------|
| **WACC = 7.1%** | $330 | $370 | $432 | $530 | $700 |
| **WACC = 7.6%** | $285 | $315 | $356 | $415 | $514 |
| **WACC = 8.1%** | $248 | $273 | $304 | $348 | $417 |
| **WACC = 8.6%** | $218 | $238 | $263 | $295 | $344 |
| **WACC = 9.1%** | $193 | $210 | $230 | $256 | $292 |

A 1% change in WACC or terminal growth moves equity value by **30–60%** — the dominant source of DCF uncertainty.

### Terminal Value Methods Compared

**1. Gordon Growth Model (most common):**
```
TV = FCFₙ₊₁ / (WACC − g)
```
Requires g < WACC (perpetuity math diverges otherwise). Long-run g should not exceed nominal GDP growth (~4–5% in 2026).

**2. Exit Multiple Method:**
```
TV = EBITDAₙ × EV/EBITDA exit multiple
```
Anchors to current market multiples — less circular if multiples are from comparable companies, but imports market sentiment into an intrinsic value calculation.

**3. H-Model (for two-stage growth):**
A closed-form approximation for fading growth: TV = FCF₀ × [(1+gL) + H × (gS − gL)] / (r − gL), where H = half-life of the high-growth period.

### Historical Case Study — Amazon DCF in 2004

In 2004, Amazon traded at ~$40/share. A naïve DCF using its then-current near-zero operating margins would have produced a value well below $40, suggesting overvaluation. Analysts who modeled the *latent profitability* — recognizing Amazon's deliberate investment phase and the operating leverage in its fulfillment and cloud infrastructure — reached values of $60–80. By 2021 Amazon peaked above $3,700 (pre-split) — a 90× gain. The lesson: DCF's quality depends entirely on the analyst's operational insight into the business model, not just mechanical number-plugging.

### Common Pitfalls and Academic Criticism

1. **Circularity in WACC**: WACC uses market-value weights, but enterprise value depends on WACC. Iterative solution required.
2. **Beta instability**: 5-year beta for growth companies is unreliable; some analysts use sector betas or fundamental betas.
3. **The Fama-French critique**: CAPM understates the cost of equity for small-cap value stocks (the size and value premiums are omitted from Kₑ).
4. **Growth optimism bias**: Sell-side DCF models systematically over-project revenue growth (Bradshaw, 2004; internal validation studies at Goldman Sachs, 2022).

---

## Related

- [[Federal-Reserve-and-Monetary-Policy]] — WACC's risk-free rate directly driven by Fed policy
- [[Modern-Portfolio-Theory]] — beta estimation and cost of equity via CAPM
- [[Black-Scholes-Option-Pricing-Model]] — real options extend DCF to handle strategic optionality
- [[Value-at-Risk-and-CVaR]] — DCF sensitivity as a form of scenario analysis

---

### Real Options Analysis, APV Method, and DCF for Pre-Revenue Companies

#### Real Options: Extending DCF to Strategic Flexibility

Standard DCF assumes a fixed investment path and discounts all cash flows at WACC. This framework systematically undervalues businesses with strategic **optionality** — the ability to expand, contract, delay, or abandon investments in response to new information. Real options analysis (ROA) applies the Black-Scholes framework to value these flexibilities as financial options.

**The Five Real Option Types:**

| Option Type | Analogy | Example |
|-------------|---------|---------|
| Option to expand | Call option | A pharma company's right to build second manufacturing line if Phase III succeeds |
| Option to abandon | Put option | An oil company's right to sell uneconomic wells if prices fall below $40/barrel |
| Option to delay | Call with exercise decision deferred | Mining company waiting for copper prices to rise before developing a marginal deposit |
| Option to stage | Compound call | Biotech funding Phase I (option to fund Phase II, which is option to fund Phase III) |
| Option to switch | Exchange option | Manufacturing plant that can switch between two products depending on relative prices |

**Valuing an expansion option (worked example):**
A pharmaceutical company has completed Phase II trials for a drug. Proceeding to Phase III requires $200M investment (exercise price K). If Phase III succeeds (probability ~30% for typical oncology drug), the drug has NPV of $1.5B; if it fails, NPV = $0. The traditional DCF:

```
Expected NPV = 0.30 × ($1.5B − $0.2B) + 0.70 × (−$0.2B) = $0.39B − $0.14B = $0.25B
```

But this ignores the option to *not* invest if Phase II results are weak. Treating Phase III funding as a call option:
- S = Current value of Phase III success payoff (NPV of $1.5B) = present value using appropriate risk-adjusted discount rate
- K = Phase III investment = $200M
- T = Time until Phase III funding decision (say, 2 years)
- σ = Volatility of biotech drug NPV (typically 60–80% for clinical-stage drugs)
- r = Risk-free rate = 4.25%

Using Black-Scholes with S₀ = $450M (PV of $1.5B × 0.30 probability ≈ $450M, risk-adjusted), K = $200M, T = 2, σ = 0.70, r = 0.0425:
```
d₁ = [ln(450/200) + (0.0425 + 0.245) × 2] / (0.70 × 1.414) = [0.810 + 0.575] / 0.990 = 1.399
d₂ = 1.399 − 0.990 = 0.409
C = 450 × N(1.399) − 200 × e^{−0.085} × N(0.409)
  = 450 × 0.919 − 191 × 0.659 = $413B − $126B = $287M
```

The real option value ($287M) exceeds the simple expected NPV ($250M) by $37M — the value of the flexibility to see additional Phase IIb data before committing Phase III capital.

**Practical limitations:** Real option analysis requires an estimate of σ for the underlying asset value — which is unobservable for private projects. Practitioners estimate σ from: (a) traded comparable company stock volatility; (b) commodity price volatility for resource projects; (c) published biotech asset volatility estimates. The framework is most useful for *ranking* investment options by optionality value rather than producing precise point estimates.

#### Adjusted Present Value (APV): Separating Operating and Financing Value

APV, developed by Stewart Myers (MIT, 1974), separates DCF valuation into:
```
APV = NPV_unlevered + PV(financing effects)
```

The unlevered NPV assumes all-equity financing at the unlevered cost of equity (removing the tax shield):
```
Unlevered NPV = FCFF / r_u (for perpetuity case)
```

Where r_u = unlevered cost of equity = r_e × [E/(E+D)] + r_d × [D/(E+D)] — essentially, WACC *without* the tax shield adjustment.

The financing effects include:
- **Tax shield PV:** PV of interest tax deductions = D × t × r_d / r_shield (if constant leverage, discount at r_d; if constant debt, discount at r_d; if leverage varies, discount at r_u)
- **Financial distress costs:** Negative NPV of expected bankruptcy/distress costs (subtracted)
- **Issue costs:** Negative NPV of underwriting/issuance costs

**When to use APV vs. WACC:**
- WACC: Target capital structure is stable over the forecast period
- APV: Highly leveraged transactions (LBOs) where debt is repaid rapidly, changing the capital structure each year; APV allows the tax shield to be discounted year-by-year at the correct rate rather than embedding it in a fixed WACC

**LBO valuation using APV:** In a leveraged buyout, $5B is acquired with $1B equity and $4B debt. If debt is repaid at $400M per year, the capital structure changes each year:

| Year | Debt | Interest (8%) | Tax Shield (21%) | PV of Shield |
|------|------|---------------|------------------|--------------|
| 1 | $4.0B | $320M | $67.2M | $65.4M |
| 2 | $3.6B | $288M | $60.5M | $57.3M |
| 3 | $3.2B | $256M | $53.8M | $49.6M |
| 4 | $2.8B | $224M | $47.0M | $42.2M |
| 5 | $2.4B | $192M | $40.3M | $35.3M |
| **Total PV of tax shields** | | | | **$249.8M** |

The APV adds this $250M tax shield PV to the unlevered business value.

#### DCF for Pre-Revenue Companies: The Venture Capital Approach

Standard DCF is poorly suited for early-stage companies with no current revenues. The venture capital (VC) method provides a practical alternative:

**VC Valuation Method:**
```
Post-Money Valuation = Terminal Value / (1 + r)^n
Pre-Money Valuation = Post-Money − Investment

Terminal Value = Revenue_exit × Exit Multiple
Return Required = (Post-Money / Investment)^{1/n} − 1 [must = required MOIC]
```

**Worked example — AI healthcare startup, Series A:**
- Revenue in 2030 (Year 5): projected $80M ARR
- Exit EV/Revenue multiple for healthcare SaaS: 8×
- Terminal Value: $640M
- Required VC return: 5× MOIC in 5 years (35% IRR)
- Post-money valuation: $640M / 5 = $128M
- Investment (Series A round): $20M
- Pre-money valuation: $128M − $20M = $108M

The "valuation" in VC is thus entirely driven by two inputs: the projected exit terminal value and the required return multiple. Both are highly uncertain, explaining why VC valuations are negotiating outcomes rather than analytical results.

**The DCF-equivalent:** If instead one applies DCF to the same startup using scenario analysis:
- Bull case (20% probability): Reaches $200M ARR, 8× exit = $1.6B exit value, PV @ 35% discount = $363M
- Base case (40% probability): $80M ARR, 8× exit = $640M, PV = $145M
- Bear case (40% probability): Company fails, value = $0

Expected value: 0.20 × $363M + 0.40 × $145M + 0.40 × $0 = $72.6M + $58M = **$130.6M post-money** — close to the VC method's $128M, validating the equivalence when probabilities and scenarios are carefully constructed.

---

## Related

- [[Federal-Reserve-and-Monetary-Policy]] — WACC's risk-free rate directly driven by Fed policy
- [[Modern-Portfolio-Theory]] — beta estimation and cost of equity via CAPM
- [[Black-Scholes-Option-Pricing-Model]] — real options extend DCF to handle strategic optionality
- [[Value-at-Risk-and-CVaR]] — DCF sensitivity as a form of scenario analysis

---

### DCF Terminal Value Sensitivity: The 80% Problem and Damodaran's Industry-Adjusted Framework

In most corporate DCF valuations, the terminal value — the estimated value of all cash flows beyond the explicit forecast period — constitutes 60-90% of total firm value. This concentration means that DCF valuations are primarily bets on terminal assumptions, not detailed near-term forecasts.

**The Terminal Value dominance — numerical demonstration:**

Consider a $10B revenue industrial company with 3% revenue growth, 12% EBIT margins, 25% tax rate, $500M annual capex, $400M D&A, and 20% change in working capital as % of revenue change. With WACC = 9% and terminal growth = 2.5%:

| Year | Free Cash Flow | PV Factor (9%) | PV of FCF |
|---|---|---|---|
| 1 | $620M | 0.917 | $569M |
| 2 | $638M | 0.842 | $537M |
| 3 | $657M | 0.772 | $507M |
| 4 | $676M | 0.708 | $479M |
| 5 | $696M | 0.650 | $452M |
| Sum of explicit period PVs | | | $2,544M |
| Terminal Value = $696M × 1.025 / (0.09 − 0.025) | | | = **$10,968M** |
| PV of Terminal Value | = $10,968M × 0.650 | | = **$7,129M** |
| **Enterprise Value** | | | **$9,673M** |

Terminal value represents $7,129M / $9,673M = **73.7% of total enterprise value** even with a mature, slow-growing industrial company. For high-growth technology companies with 10-year explicit periods, the terminal value fraction rises to 85-95%.

**The Gordon Growth Model sensitivity table for the above example:**

| WACC \ Terminal Growth | 1.5% | 2.0% | 2.5% | 3.0% | 3.5% |
|---|---|---|---|---|---|
| 8.0% | $9,804M | $10,623M | $11,619M | $12,905M | $14,694M |
| 8.5% | $9,058M | $9,673M | $10,435M | $11,409M | $12,715M |
| 9.0% | $8,387M | $8,899M | $9,501M | $10,225M | $11,127M |
| 9.5% | $7,782M | $8,213M | $8,712M | $9,292M | $9,986M |
| 10.0% | $7,233M | $7,598M | $8,013M | $8,491M | $9,049M |

Moving from the central case (WACC=9.0%, g=2.5% → $9,501M) to a moderately different assumption (WACC=8.0%, g=3.0% → $12,905M) produces a 36% increase in enterprise value — with no change in near-term forecasts. This is the "precision illusion" of DCF: the appearance of analytical rigor masks extreme sensitivity to assumptions that are themselves quite uncertain.

**Damodaran's practitioner framework — industry-specific adjustments:**

Aswath Damodaran (NYU Stern, whose publicly available valuation spreadsheets are used globally) has developed industry-specific DCF conventions:
- **Financial services companies:** Book equity is the natural base; Return on Equity − Cost of Equity is the value driver; dividend discount models or excess return models replace free cash flow approaches because "capex" and "working capital" are not meaningful for banks
- **Commodity companies:** Separate forecasts at spot commodity price (intrinsic value) vs. normalized commodity price (investment value); use of Monte Carlo simulation over commodity price distributions
- **Intangible-heavy companies:** R&D as capital investment (not expense) — add back R&D expensed, amortize it as a capital asset over the average payback period (typically 10-15 years for pharmaceutical R&D, 3-5 years for software R&D)
- **Young/startup companies:** Survivor bias correction — the expected value of the firm should be discounted for the probability of failure, which is substantial for early-stage companies regardless of optimistic base-case forecasts

**The exit multiple cross-check — preventing terminal value illusions:**

Best practice requires cross-checking the Gordon Growth terminal value against an exit multiple terminal value (EV/EBITDA × exit year EBITDA). When the two approaches diverge by more than 15-20%, the analyst should examine whether the perpetuity growth rate implies unrealistic long-run competitive dynamics. A 3% perpetuity growth rate for a company in a 2% GDP-growth economy implies the company will eventually consume all economic output — a terminal value that "makes sense" mathematically but implies increasingly implausible market dominance. The exit multiple approach anchors the terminal value in observable market pricing rather than mathematical extrapolation, providing a useful sanity check.

---

## Cross-Disciplinary Connections

### DCF ↔ Psychology: Cognitive Biases That Corrupt Forecast Quality

DCF analysis appears rigorous but is uniquely vulnerable to cognitive biases because it requires precisely the type of long-horizon, multi-variable forecasting that human cognition handles worst.

**Overconfidence in forecast precision:** Analysts constructing 5-year revenue forecasts typically present three scenarios (bear/base/bull) with the base case reflecting their genuine view. Research by Kahneman, Lovallo & Sibony (2011, *Harvard Business Review*) on the "inside view" versus "outside view" distinction shows that detailed inside-view forecasting systematically underestimates variance — the same cognitive process that makes individual analysts feel they understand a company's trajectory better than statistical base rates would suggest. The "reference class forecasting" correction (use historical base rates for comparable situations before adjusting for specifics) consistently produces more accurate DCF inputs than unaided expert judgment. See [[cognitive-biases]] for overconfidence mechanisms and [[mental-models]] for the outside-view framework.

**Anchoring on current price:** When a stock trades at $100 and an analyst builds a DCF model, the assumptions are frequently adjusted until the output is "in the right ballpark" — a process that Bradshaw (2011) showed produces DCF target prices closely correlated with current prices, defeating the purpose of independent valuation. The anchoring heuristic (described in [[prospect-theory-and-decision-making]]) causes analysts to anchor on the price they observe before constructing the model, then reverse-engineer assumptions that justify that anchor.

**Narrative fallacy in WACC construction:** The discount rate (WACC) is often calibrated to the analyst's prior belief about whether the company is a "good" or "risky" investment — a classic confirmation bias. A believer in Tesla's trajectory uses a 7% WACC; a skeptic uses 12%. The same cash flow assumptions produce a 70% difference in enterprise value from this single input. The [[behavioral-finance-and-investor-psychology]] note documents systematic overconfidence and confirmation bias in analyst forecasts, which directly undermines DCF's apparent objectivity.

### DCF ↔ Geopolitics: Country Risk Premium and Sovereign Events

DCF's WACC framework requires a country-specific risk premium for investments in emerging or frontier markets — a direct channel through which geopolitical analysis feeds into financial valuation.

**Damodaran's country risk premium (CRP) methodology:** Aswath Damodaran (NYU Stern) calculates country risk premiums as: CRP = Sovereign CDS spread × (σ_equity / σ_bonds). For June 2026: Ukraine's CRP based on war conditions ≈ 18-22%; Iran's CRP post-nuclear tensions ≈ 15-20%; Pakistan's CRP post-India-Pakistan 2025 conflict ≈ 8-12% (see [[2026-05-27-india-pakistan-2025-war-and-aftermath]]). A manufacturing company building a factory in Ukraine must add ~20% to its hurdle rate — effectively requiring a 20% annual return premium over US equivalents to justify the investment. This geopolitical risk premium channel means that any deterioration in a country's security environment directly raises the cost of capital for every investment within its borders, compressing valuations and deferring investment.

**Geopolitical scenario analysis in DCF:** The proper treatment of geopolitical risk in DCF is not a single risk-adjusted rate but scenario analysis with probability weights: a "base case" (no escalation), a "disruption case" (sanctions/conflict), and a "resolution case" (peace dividend), weighted by assessed probabilities. The [[geopolitical-risk-premium-and-markets]] note documents how geopolitical events create systematic repricing of risk premiums across entire regions.

### DCF ↔ Tech & AI: Machine Learning as an Alternative Forecasting Engine

AI models are beginning to replace or augment human-constructed DCF forecasts in quantitative equity research. Key 2026 development: Renaissance Technologies and Two Sigma have deployed transformer-based earnings forecasting models that predict near-term revenue growth more accurately than sell-side consensus estimates, with these forecasts then fed into automated DCF models. The [[machine-learning-fundamentals]] and [[transformer-architecture]] notes describe the underlying technology. The risk: ML forecasting models optimize on historical patterns and may fail during structural breaks — exactly the regime changes that make DCF forecasting hard in the first place.

---

## Related

- [[Black-Scholes-Option-Pricing-Model]] — real options extend DCF to handle strategic optionality
- [[Value-at-Risk-and-CVaR]] — DCF sensitivity as a form of scenario analysis
- [[valuation-fundamentals]] — DCF within the broader valuation toolkit
- [[Federal-Reserve-and-Monetary-Policy]] — risk-free rate and WACC set by Fed policy
- [[Modern-Portfolio-Theory]] — WACC and CAPM beta derive from MPT equilibrium
- [[macroeconomics-101]] — GDP growth rate informs terminal value perpetuity assumption
- [[private-equity-and-venture-capital]] — DCF is the core PE valuation methodology
- [[mergers-and-acquisitions]] — DCF as primary M&A valuation tool
- [[cognitive-biases]] — overconfidence and anchoring in DCF forecast construction
- [[prospect-theory-and-decision-making]] — price anchoring in analyst models
- [[behavioral-finance-and-investor-psychology]] — systematic biases in analyst DCF assumptions
- [[geopolitical-risk-premium-and-markets]] — country risk premium inputs to WACC
- [[machine-learning-fundamentals]] — AI-augmented earnings forecasting replacing manual DCF inputs
- [[2026-05-27-india-pakistan-2025-war-and-aftermath]] — country risk premium example
