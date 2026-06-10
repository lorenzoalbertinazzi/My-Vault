---
title: Value at Risk and CVaR
date: 2026-06-10
tags: [finance/risk-management, finance/quantitative, finance/derivatives, finance/regulation, finance/portfolio-management]
source: "Jorion, P. (2007). *Value at Risk: The New Benchmark for Managing Financial Risk* (3rd ed.). McGraw-Hill. Basel Committee on Banking Supervision (2019). *Minimum Capital Requirements for Market Risk* (FRTB). Bank for International Settlements."
last_updated: 2026-06-10
---

## Summary

Value at Risk (VaR) is the most widely used single-number risk metric in finance: it answers the question "how much could this portfolio lose, over a given horizon, with a given probability?" Formally, the 1-day 99% VaR is the loss level exceeded only 1% of the time. CVaR (Conditional VaR, also called Expected Shortfall or ES) addresses VaR's central flaw — it says nothing about the severity of losses beyond the threshold — by calculating the **expected loss given that VaR is breached**. Under Basel III/IV (Fundamental Review of the Trading Book, FRTB), regulators have largely replaced VaR with CVaR (ES) at the 97.5% confidence level as the primary capital metric.

---

## Key Points

- **VaR(α, T)**: loss not exceeded with probability α over horizon T
- 1-day 99% VaR ≈ 1.645× portfolio daily volatility (for a normal distribution) — or more precisely, 2.326× for 99%
- CVaR(α) = E[Loss | Loss > VaR(α)] — the conditional tail expectation
- Three estimation methods: Historical Simulation, Parametric (variance-covariance), Monte Carlo Simulation
- VaR is **not sub-additive** (Artzner et al., 1999) — diversification can appear to *increase* reported VaR for certain non-normal portfolios; CVaR is sub-additive and is therefore a "coherent risk measure"
- FRTB (Basel III market risk rules, effective 2025) mandates ES at 97.5% + stressed ES, replacing IMA VaR models

---

## Details

### Formal Definitions

Let L = portfolio loss (a random variable, positive = loss). Then:

```
VaR(α) = inf{l ∈ ℝ : P(L > l) ≤ 1−α}

CVaR(α) = E[L | L > VaR(α)] = (1/(1−α)) × ∫[VaR(α) to ∞] l·f(l)dl
```

For a standard normal loss distribution with mean μ and standard deviation σ:
```
VaR(α) = μ + z_α × σ

where z_99% = 2.326, z_97.5% = 1.960, z_95% = 1.645

CVaR(α) = μ + σ × φ(z_α)/(1−α)
```
where φ is the standard normal PDF. For α=99%: CVaR/VaR ratio ≈ 1.32 (normal distribution — this ratio grows dramatically for fat-tailed distributions).

### Three Estimation Methods

**Method 1: Parametric (Variance-Covariance)**

Assumes returns are multivariate normal. For a portfolio with weight vector **w** and covariance matrix **Σ**:
```
σₚ² = wᵀΣw
VaR(99%, 1-day) = 2.326 × σₚ × Portfolio Value
```
Fast to compute; disastrously wrong when tails are fat or during crises. Underestimated VaR by factors of 5–10 during the 2008 financial crisis.

**Method 2: Historical Simulation (HS)**

Take T days of actual P&L data. Sort losses from worst to best. The 99th percentile is the 1st percentile worst day in the sample. CVaR = average of all losses worse than VaR.

Advantages: non-parametric (captures actual tail shapes). Disadvantages: backward-looking, fully dependent on the historical window chosen, slow to incorporate new volatility regimes.

**Method 3: Monte Carlo Simulation**

Simulate thousands (often 100,000+) of portfolio scenarios using a specified return model (e.g., geometric Brownian motion, or a Gaussian copula for correlated assets). Compute portfolio P&L for each path; VaR and CVaR from the empirical distribution.

Most flexible — can handle options, path dependencies, non-linear payoffs. Computationally expensive; results depend critically on model assumptions.

### Worked Numerical Example — Equity Portfolio, June 2026

Consider a portfolio:
- **60% SPY** (S&P 500 ETF): daily vol = 0.90%, beta = 1.0
- **20% QQQ** (Nasdaq 100 ETF): daily vol = 1.20%, beta = 1.15
- **20% TLT** (20+ Year Treasury ETF): daily vol = 0.65%, beta = −0.15 (negative beta vs. equities in normal regimes)

**Total portfolio value = $10 million**

Assuming the following correlation matrix (recent 12-month realized):

|  | SPY | QQQ | TLT |
|--|-----|-----|-----|
| SPY | 1.00 | 0.92 | −0.25 |
| QQQ | 0.92 | 1.00 | −0.20 |
| TLT | −0.25 | −0.20 | 1.00 |

**Portfolio daily variance (using σ vector and correlation matrix):**

Approximate calculation:
```
σₚ² ≈ (0.6)²(0.9%)² + (0.2)²(1.2%)² + (0.2)²(0.65%)²
     + 2(0.6)(0.2)(0.92)(0.9%)(1.2%)
     + 2(0.6)(0.2)(−0.25)(0.9%)(0.65%)
     + 2(0.2)(0.2)(−0.20)(1.2%)(0.65%)

σₚ² ≈ 0.000291 + 0.0000576 + 0.0000169
     + 2(0.6)(0.2)(0.92)(0.0108%)
     + cross terms...

σₚ ≈ 0.82% per day (daily vol)
```

**1-Day Parametric VaR:**
```
VaR(99%) = 2.326 × 0.0082 × $10,000,000 = $190,732 ≈ $191,000
VaR(97.5%) = 1.960 × 0.0082 × $10,000,000 = $160,720 ≈ $161,000
CVaR(97.5%) = 1.960 × 0.0082 × 10M × [φ(1.96)/0.025]
             = $161,000 × (0.0584/0.025)
             = $161,000 × 2.336 ≈ $376,000
```

So: with 97.5% confidence, the portfolio won't lose more than **$161,000** in one day; *but* when it does breach that threshold, the expected loss is approximately **$376,000** — 2.3× the VaR figure.

### VaR's Fatal Flaw: The 2008 Financial Crisis

Prior to 2008, global banks' 99% VaR models consistently reported losses that looked manageable relative to capital buffers. On September 29, 2008 (the Monday after Lehman's bankruptcy), the S&P 500 fell **8.8%** in a single day — a move that a 99% 1-day normal VaR model would assign a probability of approximately **3 × 10⁻¹⁰** (essentially impossible in a normal lifetime of the universe). Yet it happened.

The root causes:
1. **Fat tails**: equity returns follow a Student-t distribution with approximately 4–5 degrees of freedom — far heavier tails than normal
2. **Correlation breakdown**: assets that were modeled as uncorrelated (or even negatively correlated) moved together during the crisis — "correlation goes to 1 in a crisis"
3. **Procyclicality**: banks using VaR to set position limits automatically reduced risk when VaR rose (volatility spikes) → forced selling into falling markets → amplified the crash
4. **Model gaming**: traders learned VaR thresholds and structured positions to look within limits while carrying massive tail risk (the "VaR tourism" problem)

### Coherent Risk Measures and CVaR Superiority

Artzner, Delbaen, Eber & Heath (1999) proved that VaR violates **sub-additivity**: it is possible to construct two portfolios A and B such that VaR(A + B) > VaR(A) + VaR(B). This means VaR can penalize diversification — an absurd property for a risk measure. CVaR is **coherent**: sub-additive, monotone, homogeneous, and translation-invariant. This motivated regulators to replace VaR with CVaR.

### Regulatory Framework: Basel III FRTB (2025 Implementation)

The Fundamental Review of the Trading Book (FRTB) — finalized by the Basel Committee in 2019, implemented in most G10 jurisdictions from 2025 — overhauled market risk capital:

1. **Expected Shortfall (CVaR) at 97.5%** replaces VaR at 99% as the base measure
2. **Stressed ES**: ES computed over a 12-month stressed period (e.g., 2008–2009 or 2020 COVID), reducing pro-cyclicality
3. **Internal Models Approach (IMA)**: banks must pass P&L attribution tests and backtesting requirements at the trading desk level (not just firm-wide)
4. **Standardised Approach (SA)**: a mandatory fallback and floor, substantially more conservative than the old standardized rules

The net effect: most globally systemically important banks (G-SIBs) saw market risk RWAs (risk-weighted assets) increase 30–50% under FRTB versus the Basel 2.5 rules — requiring commensurate capital increases.

### Stress Testing Beyond VaR: Scenarios and Reverse Stress Tests

**Scenario analysis**: Define a plausible adverse scenario (e.g., S&P 500 −30%, credit spreads widen 200 bps, USD appreciates 15%) and measure portfolio P&L directly. Captures correlations and non-linearities that VaR misses.

**Reverse stress test** (mandatory under EBA guidelines in the EU): Start from a loss threshold that would cause the institution to fail, and work backward to identify what market moves would cause it. Particularly valuable for identifying hidden concentrations and model blind spots.

---

## Related

- [[Modern-Portfolio-Theory]] — portfolio variance and diversification as the foundation of parametric VaR
- [[Black-Scholes-Option-Pricing-Model]] — Greeks (Delta, Gamma, Vega) feed into options VaR decomposition
- [[Federal-Reserve-and-Monetary-Policy]] — interest rate VaR, regulatory capital environment shaped by Basel
- [[Discounted-Cash-Flow-Analysis]] — scenario analysis parallels between DCF sensitivity and stress testing
