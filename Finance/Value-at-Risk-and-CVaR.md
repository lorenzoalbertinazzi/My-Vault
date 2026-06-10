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

---

### Historical Simulation VaR, Incremental VaR, and Component VaR Decomposition

#### Historical Simulation VaR: The Non-Parametric Approach

The **historical simulation** method computes VaR without assuming any return distribution. Instead, it applies the *actual* observed historical return series to the current portfolio:

**Step-by-step procedure:**
1. Collect T daily returns for each asset in the portfolio (typically T = 500 days, or about 2 years)
2. For each historical day t = 1,...,T: apply that day's percentage returns to the *current* portfolio weights and calculate the hypothetical portfolio P&L
3. Sort the T hypothetical P&L values from worst to best
4. **99% 1-day VaR** = the 5th worst loss (5th percentile = bottom 1% of 500 observations)
5. **CVaR (ES at 99%)** = average of the 5 worst losses

**Worked example (expanding the portfolio from the parametric section):**
Portfolio: $10M in 60% SPY, 20% QQQ, 20% TLT. Using 500 days of historical data (approximately January 2022 – December 2023), let's trace the actual return experience:

The 5 worst daily portfolio returns in this period would include:
- September 13, 2022 (CPI shock): SPY −4.3%, QQQ −5.2%, TLT −2.8% → portfolio −4.0%
- June 13, 2022 (Fed shock): SPY −3.9%, QQQ −4.7%, TLT −2.1% → portfolio −3.8%
- September 9, 2022: SPY −2.1%, QQQ −2.3%, TLT −1.8% → portfolio −2.1%
- ...continuing to the 5th worst observation

At 99% confidence, the historical simulation VaR would be approximately the 5th worst loss — say $380,000 for this portfolio and period. Note this can differ significantly from the parametric estimate ($191,000) computed earlier because the 2022 period contained unusually frequent large correlated drawdowns that the parametric normal distribution would underestimate.

**Advantages of historical simulation:**
- Captures actual fat tails and correlation structure experienced historically
- No distributional assumption — automatically includes non-normality, correlation breaks, and heteroscedasticity
- Regulators (Basel III IMA) prefer historical simulation over parametric for its conservative treatment of tails

**Disadvantages:**
- Dependent on the historical window: 500 days misses crises outside the window; 2500 days is computationally heavy and includes periods no longer representative
- **Ghost effect:** A crisis that falls outside the window (e.g., 2008 crisis is 18 years ago — now outside standard 500-day windows) disappears from VaR suddenly, artificially reducing estimated risk
- Equally weights each historical day — a stress day from 2022 gets the same weight as a normal day from 2021, despite the market regimes being dramatically different

**Filtered Historical Simulation (FHS):** Barone-Adesi, Bourgoin & Giannopoulos (1998) proposed filtering out the time-varying heteroscedasticity (via GARCH model) before applying historical simulation, then re-scaling the historical residuals by current conditional volatility. This hybrid approach captures fat tails while being responsive to current volatility conditions.

#### Incremental VaR: Measuring the Marginal Risk of Each Position

**Incremental VaR (IVaR)** measures how much VaR *changes* when a specific trade is added to or removed from a portfolio — the cornerstone risk metric for position limits in a trading book.

**Definition:**
```
IVaR(position i) = VaR(portfolio with position i) − VaR(portfolio without position i)
```

For a normally distributed portfolio:
```
IVaR_i ≈ (∂VaR/∂w_i) × Δw_i = z_α × (w_i × σᵢ² + Σⱼ≠ᵢ wⱼ × ρᵢⱼ × σᵢ × σⱼ) / σₚ × Δw_i
```

**Component VaR:** A complementary measure that decomposes total portfolio VaR into the contribution of each position:
```
CVaR_i = ρᵢₚ × VaR_i = (σᵢₚ / σₚ) × VaR_i
```

Where ρᵢₚ is the correlation of position i's returns with the total portfolio, and VaR_i is the standalone VaR of position i.

**Key property:** Component VaRs sum exactly to portfolio VaR:
```
Σᵢ CVaR_i = VaR_portfolio
```

This additivity makes component VaR the preferred risk decomposition for performance attribution and capital allocation.

**Worked example (three-asset portfolio):**
For our $10M portfolio (60% SPY, 20% QQQ, 20% TLT), assuming daily vols and correlations as before:

| Position | Standalone VaR | ρ with portfolio | Component VaR | % of Portfolio VaR |
|----------|---------------|-----------------|---------------|-------------------|
| SPY ($6M) | $125,600 | 0.97 | $121,832 | 63.9% |
| QQQ ($2M) | $55,980 | 0.94 | $52,621 | 27.6% |
| TLT ($2M) | $30,330 | −0.28 | −$8,492 | −4.5% |
| **Portfolio** | | | **$190,741** | **100%** |

The TLT position contributes **negative** component VaR — it actually *reduces* total portfolio risk by $8,492 despite having positive standalone risk. This is the mathematical expression of diversification. A risk manager seeing this table would know: removing TLT from the portfolio would *increase* total VaR by $8,492.

#### Marginal CVaR and Risk Budgeting

In the CVaR (Expected Shortfall) framework, the analogous decomposition is **Marginal CVaR (MCVaR)**:
```
MCVaR_i = E[Rᵢ | portfolio loss exceeds VaR threshold]
```

Summing position P&L contributions on the worst days (those where portfolio loss exceeds the VaR threshold) gives the contribution of each position to the expected tail loss. MCVaR is the preferred risk budgeting tool under Basel IV / FRTB because it uses CVaR rather than VaR as the primary risk measure.

**Risk budgeting application:** A fund manager assigns risk budgets to portfolio segments based on MCVaR — ensuring no single segment consumes more than 25% of total portfolio risk. When a position's MCVaR percentage exceeds its weight (indicating it adds disproportionate tail risk), the position is reduced or hedged. This framework transforms risk management from a monitoring function to a portfolio construction input.

---

## Related

- [[Modern-Portfolio-Theory]] — portfolio variance and diversification as the foundation of parametric VaR
- [[Black-Scholes-Option-Pricing-Model]] — Greeks (Delta, Gamma, Vega) feed into options VaR decomposition
- [[Federal-Reserve-and-Monetary-Policy]] — interest rate VaR, regulatory capital environment shaped by Basel
- [[Discounted-Cash-Flow-Analysis]] — scenario analysis parallels between DCF sensitivity and stress testing

---

### VaR's Regulatory History, the Basel Backtesting Requirement, and the 2008 Failure Autopsy

VaR's rise to regulatory dominance was driven by JP Morgan's internal risk management innovation and its subsequent adoption by the Basel Committee — a process that concentrated systemic risk in ways that VaR's inventors did not anticipate.

**The Basel Market Risk Amendment (1996) and the internal models approach:**

The original Basel Accord (1988) used simplistic standardized approaches for market risk capital — fixed percentage charges on position notionals. The 1996 Market Risk Amendment allowed banks to use internal VaR models to calculate market risk capital requirements, with the capital charge equal to 3× the 10-day 99th percentile VaR (plus a potential multiplier based on backtesting performance). This was the first time a regulatory framework explicitly authorized internal risk models for capital calculation — a profound delegation of regulatory authority to private institutions. The Basel II framework (2004) extended internal models to credit risk; the 2008 crisis validated that this delegation created dangerous incentives for model optimism.

**The Basel backtesting requirement — Green/Yellow/Red zone framework:**

Under the 1996 Amendment, banks must backtest their daily 99th percentile 1-day VaR against actual daily P&L. The expected exceedance rate: 2.5 days per year (1% × 250 trading days). The Basel traffic light framework:

| # Exceptions (250 days) | Zone | Capital Multiplier |
|---|---|---|
| 0-4 | Green | 3.0 (minimum) |
| 5 | Yellow | 3.4 |
| 6 | Yellow | 3.5 |
| 7 | Yellow | 3.65 |
| 8 | Yellow | 3.75 |
| 9 | Yellow | 3.85 |
| 10+ | Red | 4.0 (maximum) |

The limitation: the backtesting framework tests unconditional coverage (whether the average exceedance rate is correct) but not conditional coverage (whether exceptions cluster during stress periods, as they do if the model fails during crises). A model that generates 5 exceptions on 5 consecutive crisis days is treated identically to one that generates 5 exceptions spread across the year — even though the former indicates tail dependence and model failure precisely when it matters most.

**The 2008 VaR failure — forensic analysis:**

Jorion (2009, *Financial Analysts Journal*) analyzed the 2008 VaR failures at major financial institutions. Key findings: (1) Banks' reported 99th percentile 1-day VaR for their trading books was typically 0.2-0.5% of trading book notional; (2) During October-November 2008, several banks experienced daily trading losses of 2-5% of notional — 4-10× their reported 99th percentile VaR, corresponding to 99.99th percentile events or beyond on a model that assumed normal returns; (3) The fundamental model failure was correlation: positions across desks (rates, credit, equities, FX) that were modeled as moderately correlated under normal conditions became highly correlated (correlations approaching 1.0) during the crisis — the diversification that the VaR models relied on evaporated precisely when it was needed.

**The FRTB transition and CVaR mandate (Basel III/IV, effective January 2023):**

The Fundamental Review of the Trading Book (FRTB) eliminated 99th percentile VaR as the primary regulatory risk measure and replaced it with 97.5th percentile Expected Shortfall (CVaR). The regulatory rationale: CVaR's coherence property (sub-additivity — the portfolio measure can never exceed the sum of component measures) and its explicit consideration of tail shape make it a more robust risk measure during crises. FRTB additionally introduced: (1) desk-level model approval (each trading desk must pass independent backtesting to use internal models); (2) P&L attribution testing (model risk charges if modeled P&L does not track actual P&L closely enough); (3) non-modellable risk factors (risk factors with insufficient price history must be capitalized at stress-period loss estimates). The FRTB regime substantially increased risk capital requirements at large dealer banks — estimated at 30-60% increase in market risk capital — driving consolidation in market-making businesses toward the largest institutions.

---

## Cross-Disciplinary Connections

### VaR/CVaR ↔ Psychology: How Humans Psychologically Misperceive Risk Numbers

Risk managers present VaR as a single number ("our daily VaR is $50M") — but human psychological processing of risk statistics creates systematic overconfidence in what that number means.

**The VaR illusion and false precision:** Research in [[cognitive-biases]] documents the "precision heuristic" — people assign more credibility to precise numbers than to ranges, even when the precision is spurious. A VaR number expressed as "$47.3M" generates more psychological confidence than "$40-60M" despite having equivalent or greater uncertainty. This leads risk managers to mentally anchor on VaR as a loss ceiling rather than as a threshold exceeded 1% of days — a critical misinterpretation. When Deutsche Bank CRO reported a $47M daily VaR in September 2008, trading losses exceeded $1B in a single day — a 21× VaR breach that surprised management despite being theoretically possible (though with estimated probability of <10⁻¹²).

**Availability heuristic and tail risk underestimation:** Nassim Taleb's Black Swan framework (2007) is essentially an application of the availability heuristic: risk managers estimate tail event probabilities based on how easily they can recall examples, leading to dramatic underestimation of tail events that haven't occurred recently. The [[cognitive-biases]] note documents this mechanism. Practical implication: risk models calibrated on 10-year data windows (the industry standard) estimated near-zero probability for 2008-style crises in early 2007, because the 2008-scale stress had no recent precedent in the calibration window.

**Loss aversion and VaR limit-setting:** Loss aversion causes risk committees to set VaR limits conservatively (relative to expected returns) when losses are recent and liberally when gains are recent — a pattern documented by Stulz (2008) at major banks. The psychological mechanism: recent large losses increase the salience of potential future losses (availability + loss aversion), causing overly conservative risk limits that reduce profitability; recent large gains reduce this salience, causing limits to be relaxed precisely when portfolios are largest and most exposed. See [[prospect-theory-and-decision-making]] for loss aversion quantification.

### VaR/CVaR ↔ Geopolitics: Geopolitical Events as Stress Scenarios

Geopolitical events are the dominant driver of "model exception" days — VaR breaches caused by sudden, correlated moves that historical calibration did not anticipate.

**Geopolitical stress testing (2026):** Standard internal stress tests at major banks include explicit geopolitical scenarios: Taiwan Strait closure (estimates: $500B-$1T in global trade disruption, corresponding S&P 500 drawdown of 20-30%), Russia-NATO escalation (European bank exposure to frozen Russian assets crystallizes at partial recovery rates), and Iran-Hormuz closure (oil at $140-180/barrel, energy sector equity volatility spikes). Each scenario is mapped to a hypothetical P&L for all trading book positions to estimate maximum loss under the geopolitical tail. See [[2026-05-30-nato-russia-gray-zone-war]] and [[2026-06-06-iran-strait-of-hormuz-crisis-june-2026]] for current scenario parameters.

**Sanctions risk as a "non-modellable risk factor" under FRTB:** The FRTB framework's concept of non-modellable risk factors (NMRFs) — risk factors with insufficient historical price data to calibrate a model — directly captures geopolitical disruption risk. Russian ruble/asset positions post-2022 had no historical precedent for sovereign asset freeze at this scale; the FRTB rightly classified them as NMRFs requiring stress-period capital charges rather than model-based VaR.

### VaR/CVaR ↔ Tech & AI: Neural Networks for VaR Estimation

ML methods are replacing parametric and historical simulation VaR in several application areas:

**Deep learning for copula estimation:** The correlation structure between assets (the copula) is the key input to portfolio VaR. Traditional Gaussian copulas underestimate tail dependence (the tendency of assets to co-crash). ML-estimated non-parametric copulas using variational autoencoders capture tail dependence more accurately than parametric alternatives. See [[machine-learning-fundamentals]] and [[quantitative-finance-and-algorithmic-trading]].

**RL-based dynamic risk budgeting:** Reinforcement learning agents trained on historical market data learn to adjust position risk budgets in response to market regime changes — effectively implementing time-varying VaR limits. As of 2026, Citadel and Two Sigma have deployed RL-based risk management systems that dynamically tighten exposure during detected stress regimes before human risk managers would intervene. See [[reinforcement-learning-from-human-feedback]] for the RL framework.

---

## Related

- [[Modern-Portfolio-Theory]] — portfolio variance and diversification as the foundation of parametric VaR
- [[Black-Scholes-Option-Pricing-Model]] — Greeks (Delta, Gamma, Vega) feed into options VaR decomposition
- [[Federal-Reserve-and-Monetary-Policy]] — interest rate VaR, regulatory capital environment shaped by Basel
- [[Discounted-Cash-Flow-Analysis]] — scenario analysis parallels between DCF sensitivity and stress testing
- [[quantitative-finance-and-algorithmic-trading]] — algorithmic implementation of VaR and risk management
- [[hedge-funds-and-alternative-strategies]] — VaR limits as hedge fund portfolio management tools
- [[credit-markets-and-credit-risk]] — credit VaR and the role of default correlation in tail risk
- [[fixed-income-deep-dive]] — duration/convexity as linear/quadratic risk measures analogous to Delta/Gamma
- [[cognitive-biases]] — precision heuristic and availability bias in risk misperception
- [[prospect-theory-and-decision-making]] — loss aversion distorting risk limit-setting
- [[behavioral-finance-and-investor-psychology]] — systematic behavioral failures in institutional risk management
- [[geopolitical-risk-premium-and-markets]] — geopolitical stress scenarios in VaR models
- [[2026-06-06-iran-strait-of-hormuz-crisis-june-2026]] — current geopolitical VaR stress scenario
- [[machine-learning-fundamentals]] — ML copula estimation and RL risk management
- [[reinforcement-learning-from-human-feedback]] — RL for dynamic VaR-based position sizing
