---
title: Black-Scholes Option Pricing Model
date: 2026-06-10
tags: [finance/derivatives, finance/options, finance/quantitative, finance/risk-management, mathematics/stochastic-calculus]
source: "Black, F. & Scholes, M. (1973). The Pricing of Options and Corporate Liabilities. *Journal of Political Economy*, 81(3), 637–654. Merton, R.C. (1973). Theory of Rational Option Pricing. *Bell Journal of Economics*, 4(1), 141–183."
last_updated: 2026-06-10
---

## Summary

The Black-Scholes model (1973) provides a closed-form solution for the fair value of a European option on a non-dividend-paying stock. It revolutionized derivatives markets by showing that option prices depend on only five observable inputs — stock price, strike price, time to expiration, risk-free rate, and implied volatility — and that a continuously rebalanced delta-hedge eliminates all market risk, leaving only the risk-free return. The model earned Fischer Black (posthumously) and Myron Scholes the 1997 Nobel Prize in Economics (shared with Robert Merton, who extended the framework).

---

## Key Points

- The call price is: **C = S₀N(d₁) − Ke^(−rT)N(d₂)**
- The put price (by put-call parity): **P = Ke^(−rT)N(−d₂) − S₀N(−d₁)**
- Implied volatility (σ_IV) is the market's consensus forecast of future realized volatility, backed out by inverting the formula
- The "Greeks" (Δ, Γ, Θ, Vega, ρ) quantify sensitivities to each input and are the core tools of options risk management
- Key assumptions — lognormal returns, constant volatility, continuous trading, no dividends, no transaction costs — are all violated in practice, giving rise to a rich literature of extensions

---

## Details

### The Five Inputs and Their Roles

| Symbol | Input | Typical effect on call value |
|--------|-------|------------------------------|
| S₀ | Current stock price | ↑ S → ↑ C (more intrinsic value) |
| K | Strike price | ↑ K → ↓ C (higher hurdle) |
| T | Time to expiration (years) | ↑ T → ↑ C (more time for S to rise) |
| r | Continuously compounded risk-free rate | ↑ r → ↑ C (lower PV of K) |
| σ | Volatility of returns (annualized) | ↑ σ → ↑ C (wider distribution of outcomes) |

### The Full Formula with d₁ and d₂

Given S₀ = current stock price, K = strike, r = risk-free rate, σ = volatility, T = time in years:

```
d₁ = [ln(S₀/K) + (r + σ²/2)T] / (σ√T)
d₂ = d₁ − σ√T
```

N(·) is the standard normal CDF. Intuitively, N(d₂) is the risk-neutral probability the option expires in-the-money; N(d₁) is the delta — the hedge ratio telling you how many shares to hold to replicate the option.

### Worked Numerical Example — Apple Inc. (AAPL), June 2026

Suppose AAPL trades at **S₀ = $220**, you price a call with **K = $225**, expiring in **T = 0.25 years** (≈3 months). The 3-month Treasury yield is **r = 4.25%** (annualized, continuously compounded ≈ 4.16%). Historical 30-day realized vol has been running around **σ = 28%**.

```
d₁ = [ln(220/225) + (0.0416 + 0.28²/2) × 0.25] / (0.28 × √0.25)
   = [ln(0.9778) + (0.0416 + 0.0392) × 0.25] / (0.28 × 0.5)
   = [−0.02247 + 0.0202] / 0.14
   = −0.00227 / 0.14
   = −0.01621

d₂ = −0.01621 − 0.14 = −0.15621
```

Looking up the standard normal CDF:
- N(d₁) = N(−0.016) ≈ 0.4936
- N(d₂) = N(−0.156) ≈ 0.4380

```
C = 220 × 0.4936 − 225 × e^(−0.0416×0.25) × 0.4380
  = 108.59 − 225 × 0.9897 × 0.4380
  = 108.59 − 97.48
  = $11.11
```

A market-maker would quote this call near **$11.11**. Delta = 0.4936 means for every 100 call contracts (10,000 shares notional) sold, the dealer needs to buy approximately **4,936 shares** to be delta-neutral.

### The Greeks in Detail

**Delta (Δ = ∂C/∂S):** For our example, Δ ≈ 0.49. If AAPL moves from $220 to $221, the call gains roughly $0.49. Delta ranges 0–1 for calls, −1 to 0 for puts.

**Gamma (Γ = ∂²C/∂S²):** The rate of change of delta. Gamma is highest for at-the-money options near expiry — this is where delta-hedging is most expensive because the hedge must be rebalanced most frequently. Gamma = N'(d₁)/(S₀σ√T).

**Theta (Θ = ∂C/∂T):** Time decay. An ATM 3-month option loses approximately Θ = −C × ... roughly $0.05–0.10/day near expiry. Option sellers benefit; buyers pay.

**Vega (∂C/∂σ):** Sensitivity to volatility. Vega = S₀√T × N'(d₁). For our example: Vega = 220 × 0.5 × N'(−0.016) ≈ 220 × 0.5 × 0.3989 ≈ **$43.9 per 1% change in σ** (per 100 shares). This is why options desks obsess over the "vol surface."

**Rho (∂C/∂r):** Sensitivity to interest rates. For short-dated equity options, rho is small. For long-dated LEAPS, rho can be significant — a 1% rate rise raises a 2-year ATM call noticeably.

### The Volatility Surface and the Smile

The single most important empirical failure of Black-Scholes is that implied volatility is **not constant** across strikes and maturities. Plotting σ_IV vs. strike for a fixed expiry reveals a "smile" or "skew" — deep out-of-the-money puts trade at higher implied vol than ATM options. This reflects:
1. **Fat tails / crash risk** — equity returns are more negatively skewed and leptokurtic than lognormal
2. **Leverage effect** — stock prices and volatility are negatively correlated
3. **Jump risk** — discrete crashes (e.g., 2008, March 2020, August 2024 carry unwind) are not captured by Brownian motion

Practitioners address the smile with **local volatility** models (Dupire, 1994; Derman & Kani, 1994), **stochastic volatility** models (Heston, 1993; SABR), and **jump-diffusion** models (Merton, 1976; Kou, 2002). Despite its shortcomings, Black-Scholes remains the industry-standard **quoting convention** — everyone uses it to translate market prices into implied vol and then trades deviations from that baseline.

### Historical Context: CBOE and the Birth of Listed Options

The Chicago Board Options Exchange opened on **April 26, 1973** — the same year the Black-Scholes paper was published. In the first year, just 911 contracts changed hands daily on 16 stocks. By 2025, daily US equity options volume routinely exceeded **50 million contracts**. The model didn't just describe a market; it helped create one by giving market-makers a systematic way to hedge.

### The Long-Term Capital Management (LTCM) Lesson — 1998

LTCM, co-founded by Scholes and Merton themselves, used options-based relative-value strategies with extreme leverage (at peak: $125 billion in assets on $4.7 billion of equity — 26:1 leverage, with off-balance-sheet derivatives notional exceeding $1.25 trillion). When Russia defaulted in August 1998 and correlations across asset classes collapsed to extreme co-movements that Black-Scholes models treated as near-impossible, LTCM lost $4.6 billion in under four months and required a $3.6 billion Federal Reserve–orchestrated private bailout. The episode remains the canonical demonstration that model-based hedging is only as good as the model's assumptions about tail risk.

### Current Developments — 2026

Quantitative desks are increasingly replacing Black-Scholes implied vol with **rough volatility** models (Gatheral, Jaisson & Rosenbaum, 2018), which model σ itself as a fractional Brownian motion with Hurst parameter H ≈ 0.1 — far rougher than standard BM (H = 0.5). These models fit the volatility term structure and the at-the-money volatility skew simultaneously with far fewer parameters. Machine-learning approaches (neural SPDEs, deep hedging by Bühler et al., 2019) are also moving from research into production at major dealers as of 2025–2026.

---

## Related

- [[Modern-Portfolio-Theory]] — portfolio construction context for options overlays
- [[Value-at-Risk-and-CVaR]] — how options Greeks feed into VaR calculations
- [[Federal-Reserve-and-Monetary-Policy]] — interest rate sensitivity (Rho, term structure)
- [[Discounted-Cash-Flow-Analysis]] — risk-free rate assumptions shared with DCF
