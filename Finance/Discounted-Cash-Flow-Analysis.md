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
