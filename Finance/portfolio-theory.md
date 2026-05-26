---
title: Portfolio Theory and Risk Management
date: 2026-05-26
tags: [finance, portfolio, risk, MPT, investing]
source: research
last_updated: 2026-05-26
---

## Summary
Portfolio theory answers the question: how should you combine assets to maximize return for a given level of risk? The core insight from Modern Portfolio Theory (Markowitz, 1952) is that diversification reduces risk without proportionally reducing expected return — because assets don't move in perfect lockstep. Risk management is not about avoiding risk entirely, but about being compensated for it.

## Key Points
- **Diversification** eliminates idiosyncratic (company-specific) risk; only systematic (market) risk remains
- **Correlation** is the key variable — low-correlation assets provide the most diversification benefit
- **Sharpe ratio** measures return per unit of risk — the key metric for comparing portfolios
- **Beta** measures sensitivity to the broad market; alpha is excess return beyond what beta explains
- Asset allocation (stocks/bonds/alternatives) drives ~90% of long-run portfolio returns

## Details

### Risk: Systematic vs. Idiosyncratic
- **Idiosyncratic risk**: Company-specific (a CEO scandal, a product recall). Eliminated by diversification.
- **Systematic risk (market risk)**: Risk that affects all assets (recessions, rate shocks). Cannot be diversified away.

Adding ~20–30 uncorrelated stocks eliminates most idiosyncratic risk. Beyond that, adding more stocks doesn't significantly reduce portfolio volatility.

### Correlation and Diversification
The portfolio variance formula: **σ²p = w₁²σ₁² + w₂²σ₂² + 2w₁w₂σ₁σ₂ρ₁₂**

Where ρ is correlation (−1 to +1).
- If ρ = 1: No diversification benefit (assets move identically)
- If ρ = 0: Full diversification of idiosyncratic risk
- If ρ = −1: Perfect hedge (rare in practice)

**Classic diversifiers**: Bonds vs. equities (historically negative correlation during normal regimes). Gold. Volatility (VIX products).

### The Efficient Frontier
Every combination of assets has a risk/return profile. The **efficient frontier** is the set of portfolios that maximize return for each level of risk. No rational investor should hold a portfolio inside (below) the frontier.

The **Market Portfolio** (Sharpe) is the tangency point where a line from the risk-free rate touches the frontier — it has the highest Sharpe ratio. In theory, this is the entire market-cap-weighted market (the basis for index investing).

### Key Metrics

**Sharpe Ratio**: (Portfolio Return − Risk-Free Rate) / Portfolio Std Dev  
Higher = better risk-adjusted return. A Sharpe above 1.0 is considered good; above 2.0 is excellent and rare.

**Sortino Ratio**: Like Sharpe but only penalizes downside volatility. More practical because investors care about losses, not upside volatility.

**Beta (β)**: Sensitivity to market moves. β = 1.0 → moves with market. β = 1.5 → 50% more volatile. β = 0 → uncorrelated.

**Alpha (α)**: Return above what beta would predict. True alpha is rare — most "alpha" is just hidden beta (factor exposures).

**Maximum Drawdown**: Largest peak-to-trough loss. Critical for understanding real-world pain, especially for leveraged or concentrated positions.

### Factor Investing (Beyond CAPM)
Fama-French and others identified additional risk factors beyond market beta that explain returns:

| Factor | Premium | Explanation |
|--------|---------|-------------|
| **Value** | Small-cap value outperforms | Distressed firms carry extra risk |
| **Size** | Small caps outperform | Less liquidity, more risk |
| **Momentum** | Recent winners keep winning | Behavioral: slow reaction to news |
| **Profitability** | High-profit firms outperform | Quality premium |
| **Low Volatility** | Low-vol stocks outperform risk-adjusted | Behavioral anomaly |

### Asset Allocation Frameworks

**60/40 (Traditional)**: 60% stocks / 40% bonds. Worked well for decades when stocks and bonds were negatively correlated. Stress-tested in 2022 when both fell simultaneously.

**Risk Parity**: Allocate by risk contribution, not dollar weight. Bonds (lower vol) get more weight to match equities' risk contribution. Requires leverage on bonds.

**Permanent Portfolio (Harry Browne)**: 25% each in stocks, bonds, gold, cash. Designed to survive any economic regime.

**Barbell**: Heavy allocation to safe assets (short-term bonds) + small allocation to high-risk/high-reward (options, venture). Avoids the "middle" (medium-risk assets that are neither safe nor explosive).

### Position Sizing
**Kelly Criterion**: Optimal fraction to bet = (Edge / Odds). Maximizes long-run growth but is aggressive — practitioners use half-Kelly or quarter-Kelly to reduce volatility of outcomes.

**Concentration vs. Diversification tradeoff**: Buffett/Munger favor concentration (know a few things well); institutional investors favor diversification (can't know everything). The right answer depends on your informational edge.

## Related
- [[valuation-fundamentals]]
- [[macroeconomics-101]]
- [[options-basics]]
