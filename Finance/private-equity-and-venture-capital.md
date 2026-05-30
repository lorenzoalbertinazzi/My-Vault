---
title: Private Equity & Venture Capital
date: 2026-05-30
tags: [finance, private-equity, venture-capital, leveraged-buyout, fund-structure, carried-interest, J-curve, valuation, LP-GP]
source: Kaplan & Strömberg (2009) Leveraged Buyouts and Private Equity; Metrick & Yasuda (2010) The Economics of Private Equity Funds; Gompers & Lerner (2001) The Venture Capital Cycle; Preqin Global Private Equity Report 2024
last_updated: 2026-05-30
---

## Summary
Private equity (PE) and venture capital (VC) are investment strategies that deploy capital into private (non-publicly-traded) companies, seeking outsized returns through ownership, operational improvement, and eventual liquidity events (IPO, acquisition, or secondary sale). The industry manages approximately $8 trillion in assets globally as of 2024, making it one of the most consequential forces in corporate finance. PE generally targets mature companies using leverage (debt) to amplify returns; VC targets early-stage companies accepting high failure rates in exchange for exposure to potential 100x winners. Both operate through a distinctive fund structure in which professional managers (General Partners, GPs) invest capital raised from institutional investors (Limited Partners, LPs) and are compensated primarily through performance fees (carried interest).

## Key Points
- The standard fee structure — "2 and 20" (2% management fee, 20% carried interest) — concentrates economic incentives heavily on exit performance
- Leveraged buyouts (LBOs) amplify equity returns through debt: a 3x MOIC on assets becomes a 5–6x MOIC on equity via leverage
- VC follows a power-law return distribution: the top decile of investments (typically 1–2 per portfolio of 20–30) generate nearly all the fund's profits
- The J-curve effect means PE/VC funds show negative reported returns in early years (fees + write-downs) before turning positive — a critical feature for LP cash flow planning
- The Internal Rate of Return (IRR) metric is systematically gamed by timing of capital calls; Public Market Equivalent (PME) has emerged as the preferred benchmark
- Top-quartile PE funds have historically returned net IRRs of 15–20% vs. S&P 500's ~10%, but the dispersion is enormous and top-quartile status is moderately persistent

## Details

### Fund Structure and Legal Architecture

The standard private equity/VC fund is structured as a **Delaware Limited Partnership**, chosen for its tax transparency, liability protection, and flexible governance terms:

```
                    ┌──────────────────┐
                    │  General Partner │  ← Investment firm (e.g., KKR, Sequoia)
                    │     (GP)        │
                    └────────┬─────────┘
                             │ Manages & invests
                    ┌────────▼─────────┐
                    │   Fund LP         │  ← The investment vehicle
                    │ (e.g., Sequoia   │
                    │  Capital XIV LP) │
                    └────────┬─────────┘
                             │ Capital
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
         ┌────────┐    ┌────────┐    ┌────────┐
         │Pension │    │Endow-  │    │Sovereign│
         │ Fund   │    │ment    │    │Wealth  │
         │  (LP)  │    │  (LP)  │    │Fund(LP)│
         └────────┘    └────────┘    └────────┘
```

**LP Commitments:** LPs commit capital at fund close but do not transfer it immediately. The GP draws down capital over a **3–5 year investment period** via capital calls as investments are identified.

**Fund Life:** Typically **10 years** (investment period + harvest period), with two possible 1-year extensions. Extensions require LP consent.

**GP Commitment:** GPs typically commit 1–5% of total fund size from their own capital, aligning incentives with LPs ("skin in the game"). For a $5B fund, this is $50–250M.

### Fee Economics: The "2 and 20" Model

#### Management Fee
- **Rate:** Typically 1.5–2.0% per year of **committed capital** during investment period, then 1.0–1.5% of **invested capital** (or NAV) during harvest period
- **Purpose:** Covers operating expenses: salaries, travel, deal sourcing, portfolio monitoring, fund administration
- **Scale:** $1B fund at 2% = $20M/year. For a large PE firm managing $50B across multiple funds, management fees alone generate $500M–$1B/year in predictable, fee-like revenues — which is why PE firms like KKR, Blackstone, and Apollo have gone public

#### Carried Interest ("Carry")
- **Rate:** Standard 20% of profits above the **hurdle rate** (preferred return, typically 8% IRR)
- **Mechanism (European/whole-fund waterfall):** LPs receive 100% of distributions until they've recovered their invested capital + 8% annual return ("hurdle"). Then GP receives 100% of subsequent distributions ("catch-up") until it has received 20% of total profits. Thereafter: 80% to LPs / 20% to GPs
- **Example:** Fund invests $1B, returns $2.5B:
  - LPs invested $1B, hurdle at 8% over 5 years = $1.47B
  - GP catch-up: 20% of ($2.5B - $1.47B) = $206M catch-up
  - Remaining profits: ($2.5B - $1.47B - $206M) × 20% = $165M
  - Total carry to GP: ~$371M on a $1B fund — extraordinary economics
- **Taxation controversy:** Carried interest is taxed as long-term capital gains (20% + 3.8% NIIT = 23.8%) rather than ordinary income (37%) in the US — a political flashpoint since carried interest is effectively labor income

#### Hurdle Rate and Preferred Return
The **preferred return (pref)** of 8% means GPs only share in profits after LPs have earned their money back plus 8% compound annual growth. This creates a floor for LP protection. In practice, the pref creates powerful incentives:
- GPs rush capital deployment to start the clock
- Exit timing is influenced by IRR calculations

### Leveraged Buyout (LBO) Mechanics

An LBO acquires a company using a small equity contribution (~30–40%) and a large debt stack (~60–70%), secured against the target's assets and cash flows. The debt amplifies equity returns when the investment succeeds — and destroys equity when it fails.

**LBO Return Waterfall:**

Consider KKR's acquisition of Dell Technologies' infrastructure business (hypothetical illustration):
```
Enterprise Value (EV) at acquisition:        $10,000M
Equity contribution (30%):                    $3,000M
Debt (LBO facilities):                        $7,000M
 ├── Senior Secured Term Loan B (4.5%):       $4,000M
 ├── Second Lien (7.5%):                      $2,000M
 └── Subordinated / Mezzanine (12%):          $1,000M

5-Year Hold: EBITDA grows from $1,000M to $1,500M
EV at exit (same 10x EBITDA multiple):       $15,000M
Debt repaid (from FCF during hold):          -$3,000M
Remaining debt:                              -$4,000M
Equity value at exit:                        $8,000M

Equity MOIC: $8,000M / $3,000M = 2.67x
Equity IRR over 5 years: (2.67)^(1/5) - 1 = 21.7%

If no leverage (all equity, same EV growth):
  $15,000M / $10,000M = 1.5x MOIC → 8.4% IRR
```

**Three sources of PE returns (Kaplan & Strömberg 2009):**
1. **Multiple expansion:** Buying at 8x EBITDA, selling at 12x (market re-rating, improved business quality)
2. **Earnings growth:** Operational improvement, cost cuts, add-on acquisitions
3. **Debt paydown:** FCF reduces debt, increases equity — effectively forced savings via leverage

**Historical attribution (2000–2015 sample):** Multiple expansion contributed ~30%, earnings growth ~50%, debt paydown ~20% of total returns.

### Venture Capital: The Power Law Economy

VC operates in a fundamentally different mathematical regime. Peter Thiel articulates the VC power law: the best investment in a portfolio outperforms all others combined; the best fund outperforms all others combined. This is not hyperbole — it is empirically documented.

**Typical VC fund portfolio (Seed/Series A):**
```
20–25 investments of $500K–$5M each
Typical outcomes:
  ~40% total loss (company fails)
  ~30% modest loss (1x or less)
  ~20% moderate return (1–3x)
  ~8% good return (3–10x)
  ~2% exceptional (>30x)

The ~2% (1 investment in 50) must return the entire fund
```

**The "fund returner" math:**
- $100M fund with 20 investments at $5M average
- To return 3x ($300M) in 10 years, need companies worth $300M at exit
- Single company must reach ~$1B+ valuation (unicorn) for investment to "return the fund" (given 20–30% ownership diluted from follow-ons)
- Sequoia Capital's investment in Stripe (2012, ~$3M for ~10% at ~$30M valuation) returned >$10B at Stripe's $95B peak valuation — returning their fund ~100x

**Portfolio construction theory:**
Chris Dixon (a16z): "The biggest secret in venture capital is that the best investment in a successful fund equals or outperforms the entire rest of the fund combined." (Dixon & Singh, blog post, 2012)

**Power-law math:** If returns follow a Pareto distribution with shape parameter α ≈ 1.5 (consistent with empirical VC data), then:
- Top 10% of investments generate ~70% of total returns
- Bottom 50% generate ~5% of total returns
- Implication: portfolio diversification is LESS important than getting into the best companies; VC is about picking, not diversifying

### Deal Stages and Valuation

#### VC Funding Stages
| Stage | Typical Size | Valuation | Use of Capital |
|---|---|---|---|
| Pre-seed/Friends & Family | $50K–$500K | $1–5M | MVP, validation |
| Seed | $500K–$3M | $3–15M | Product-market fit |
| Series A | $2–20M | $15–60M | Scale growth |
| Series B | $15–75M | $60–250M | Market expansion |
| Series C+ | $50M–$500M+ | $250M–$5B | Dominance, pre-IPO |
| Unicorn | — | $1B+ | — |

#### VC Valuation Method
The **VC Method (Sahlman 1990):**

```
Step 1: Estimate terminal value at exit (Year 7–10)
  Revenue at exit × Industry P/S multiple = Exit EV
  
Step 2: Discount back to present
  PV = Exit EV / (1 + VC target IRR)^n
  Target IRR typically 40–60% (compensates for ~50% failure rate)
  
Step 3: Calculate ownership required
  Investment / PV = Required ownership % pre-dilution
  
Step 4: Adjust for dilution
  Required ownership × dilution factor = Current ownership stake
```

**Example:**
- SaaS startup, $1M ARR growing 150%/year
- Exit Year 6 projected: $50M ARR × 10x revenue multiple = $500M EV
- Target IRR: 50%
- PV = $500M / (1.5)^6 = $500M / 11.39 = $43.9M post-money valuation
- $5M investment buys $5M / $43.9M = 11.4% pre-dilution ownership

#### PE LBO Valuation
PE uses DCF, comparables (EV/EBITDA multiples vs. public comps and precedent transactions), and the **LBO model** (what price can I pay and still hit 20%+ IRR given the debt stack and projected cash flows?). The LBO model is the binding constraint — it sets the maximum bid price.

### The J-Curve

The **J-curve** describes fund NAV trajectory over its life:

```
Year:        1    2    3    4    5    6    7    8    9   10
NAV/Cost:  -15% -12%  -5%   5%  20%  45%  75% 110% 160% 200%
```

Early years show negative returns because:
1. Management fees reduce NAV before exits occur
2. Write-downs are booked immediately; write-ups only when exits materialize
3. Portfolio companies are actively investing (cash outflow) rather than distributing

Implication: LPs who sell PE fund interests in the secondary market in Years 1–3 pay significant discounts (sometimes 40–60% to NAV). The secondary PE market has grown to ~$130B in annual transaction volume (2023), pricing and transmitting this information.

### Return Measurement: IRR vs. PME

**IRR (Internal Rate of Return):** The discount rate that makes NPV of all cash flows = 0. Standard PE reporting metric.

**Why IRR misleads:**
- IRR **implicitly assumes** reinvestment of distributions at the same rate — impossible in practice
- Short-duration, quick-flip deals generate astronomical IRRs (300% on a 90-day deal = 300%, but only $1M profit)
- GPs can **"game" IRR** by borrowing (subscription lines) to delay capital calls — if you fund an investment 6 months late, IRR improves without changing actual returns

**MOIC (Multiple on Invested Capital):** Total return in dollar terms. $1M → $3M = 3x MOIC. Does not account for time value of money.

**PME (Public Market Equivalent, Long-Nickels 1996):**
Answers: "What would LP returns have been if they'd invested in the S&P 500 on the same capital call/distribution schedule?" PME > 1.0 means PE outperformed. More honest because it accounts for time value and market beta.

**Empirical performance (Harris, Jenkinson & Kaplan 2014):**
- US buyout funds: PME ~1.27 (outperformed S&P by ~27% on invested capital) for vintages 1980–2010
- US VC funds: PME ~1.42 (much higher but much greater dispersion)
- Since 2010: debate continues; some studies show declining PME as competition for assets intensified

### Major Players and Notable Deals

#### Private Equity — Mega Buyout Funds
| Firm | AUM (2024) | Founded | Key Strategy |
|---|---|---|---|
| Blackstone | ~$1T | 1985 | Diversified PE, real estate, credit |
| Apollo Global | ~$650B | 1990 | Credit-driven PE, distressed |
| KKR | ~$600B | 1976 | LBO pioneer, now diversified |
| Carlyle | ~$435B | 1987 | Defense/aerospace, diverse sectors |
| Warburg Pincus | ~$85B | 1966 | Growth equity, healthcare |

**Landmark deals:**
- **RJR Nabisco LBO (1989, KKR, $31.4B):** "Barbarians at the Gate" era. First mega-LBO. KKR ultimately earned modest returns after mismanagement and rising rates
- **Hilton Hotels (2007, Blackstone, $26B):** Bought at peak; near-bankruptcy in 2009; went public in 2013 at massive profit ($14B return on $5.7B equity — 2.5x MOIC, 40% IRR). Strategic asset management rather than just financial engineering
- **Dell Technologies (2013, Silver Lake + Michael Dell, $24.4B):** Took Dell private; transformed from declining PC maker to enterprise infrastructure leader; returned to public markets 2018 at far higher valuation

#### Venture Capital
| Firm | AUM | Notable Investments |
|---|---|---|
| Sequoia Capital | ~$85B | Apple, Google, WhatsApp, Stripe, Airbnb |
| Andreessen Horowitz (a16z) | ~$42B | Facebook, GitHub, Lyft, Coinbase, OpenAI |
| Accel | ~$22B | Facebook (early), Dropbox, Spotify |
| Benchmark | ~$5B | eBay, Twitter, Uber, Snap, Discord |
| Founders Fund | ~$11B | SpaceX, Palantir, Airbnb, Stripe |

**Benchmark's investment in Uber (2011):** $12M for ~20% at $37M valuation. At Uber's $82B IPO valuation: return of ~$7.5B on $12M — approximately 625x MOIC, the most profitable venture investment in history at that point.

### Due Diligence Process

**VC Due Diligence (4–8 weeks for Series A):**
1. **Team:** Prior founder experience, domain expertise, reference checks with ex-colleagues
2. **Market:** TAM/SAM/SOM analysis; market sizing via bottom-up demand modeling
3. **Product:** Demo, user interviews, retention metrics (Day 30/Day 90 cohort retention), NPS
4. **Business model:** Unit economics — CAC (Customer Acquisition Cost), LTV (Lifetime Value), LTV/CAC > 3x is minimum bar; > 5x is strong
5. **Competition:** Moat analysis — network effects, switching costs, proprietary data, brand
6. **Financials:** Burn rate, runway, revenue quality, churn

**PE Due Diligence (6–16 weeks for LBO):**
1. **Quality of Earnings (QoE):** Third-party accounting firm verifies EBITDA adjustments; one-time items vs. recurring; GAAP-to-Adjusted-EBITDA bridge
2. **Commercial DD:** Strategy consultant assesses market size, competitive position, customer concentration risk
3. **Operational DD:** Operations consultant benchmarks margins vs. peers; identifies $X of cost savings
4. **Legal DD:** Material contracts, IP ownership, litigation exposure, regulatory compliance
5. **Financial modeling:** 5-year operating projections; downside case; LBO model sensitivity analysis

### Misconceptions and Edge Cases

**Misconception 1: "Private equity always destroys jobs."**
Evidence is mixed. Kaplan & Strömberg (2009) find PE-backed companies show faster employment growth than public peers in the first 2 years post-buyout, with temporary net job losses in restructuring deals. The sign of the effect depends on whether the acquisition is a growth buyout (job creator) or a distress/asset-strip situation (job destroyer).

**Misconception 2: "VC selects the best founders."**
Research shows significant demographic bias: 77% of VC investments go to white founders, 2% to Black founders (NVCA 2023). Female-led startups receive ~2% of VC dollars while comprising ~20% of applicants. Studies (Kanze et al. 2018) show VCs ask men promotion-focused questions and women prevention-focused questions, generating systematically different outcomes.

**Misconception 3: "High IRR = good investment."**
A fund that returned 25% IRR but took 5 years on $1M investments generated far less value than one that returned 15% IRR on $1B over 10 years. IRR is scale-agnostic. Always consider MOIC and total value to paid-in (TVPI).

**Edge case: Distressed PE (Special Situations):**
Apollo, Oaktree (Howard Marks), and Elliott Management specialize in **distressed debt** — buying bonds of bankrupt or near-bankrupt companies at 30–50 cents on the dollar, then converting to equity through bankruptcy proceedings. Returns require deep legal expertise in US Bankruptcy Code (Chapter 11 process, absolute priority rule). Oaktree's Distressed Debt strategy has returned 19% net IRR since 1988.

### Cross-Disciplinary Connections

- **Game theory:** LP-GP relationship is a principal-agent problem; fee structure design (carry hurdles, clawbacks) are mechanism design solutions to minimize moral hazard
- **Organizational behavior:** Portfolio company transformations require change management; PE's 100-day plan literature parallels McKinsey's organizational turnaround playbooks
- **Network science:** VC deal flow follows power-law networks — top founders were funded by top VCs (Sequoia → PayPal → PayPal Mafia → all subsequent Musk/Thiel ventures). Warm introductions drive 90%+ of deal flow at top firms

## Related
- [[portfolio-theory]] — Asset allocation frameworks; PE as an alternative asset class
- [[valuation-fundamentals]] — DCF and comparables used in PE/VC
- [[behavioral-finance-and-investor-psychology]] — LP herd behavior, endowment effect in fund selection
- [[options-basics]] — Convertible notes (SAFE, convertible debt) used in early-stage VC have option-like payoffs
- [[macroeconomics-101]] — Interest rate cycles profoundly affect LBO viability and PE entry multiples
- [[quantitative-finance-and-algorithmic-trading]] — Secondary PE market analytics; quantitative approaches to LP portfolio construction
