---
title: Securitization, ABS, MBS, and Structured Products
date: 2026-06-06
tags: [finance, securitization, structured-products, ABS, MBS, CDO, CLO, credit, fixed-income, financial-crisis]
source: BIS Quarterly Review, SIFMA, IMF Global Financial Stability Reports, Gorton & Metrick (2012)
last_updated: 2026-06-09
---

## Summary
Securitization is the process of pooling financial assets — mortgages, auto loans, credit card receivables, corporate loans — and issuing tradeable securities backed by those pools. It is one of the most powerful and most dangerous financial innovations of the 20th century: it enabled the democratization of credit, reducing borrowing costs for millions, while simultaneously creating the conditions for the 2007–2009 Global Financial Crisis, the worst economic catastrophe since the Great Depression. Understanding securitization is essential to understanding modern credit markets, systemic risk, and the regulatory architecture built in its aftermath.

## Key Points
- Global ABS/MBS outstanding exceeded $13 trillion in 2025, with US MBS markets alone at ~$12 trillion
- Securitization decouples loan origination from credit risk retention — enabling massive credit expansion and its associated fragilities
- Tranching creates senior/mezzanine/equity structures that redistribute (but do not eliminate) risk
- The 2008 financial crisis was fundamentally a securitization crisis: CDO² structures amplified losses through the entire financial system
- Post-crisis reforms (Dodd-Frank risk retention, EU Securitization Regulation) reshaped the market
- CLOs (Collateralized Loan Obligations) are the dominant structured credit vehicle in 2025–2026, with $1.3 trillion outstanding in the US
- Agency MBS (Fannie Mae, Freddie Mac, Ginnie Mae) carry implicit government guarantee; non-agency lacks this backstop

## Details

### Mechanism: How Securitization Works

The securitization process has five core structural components:

**1. Asset Origination:** A bank or financial institution originates loans — mortgages, car loans, student loans, credit card balances. These are the raw material.

**2. Special Purpose Vehicle (SPV) / Special Purpose Entity (SPE):** The originator sells the assets to a bankruptcy-remote SPV — a legal entity specifically created to hold the assets, isolated from the originator's balance sheet. This isolation means if the originator goes bankrupt, creditors cannot claim the securitized assets. The SPV is typically a trust or LLC with no employees or operations.

**3. Tranching:** The SPV issues multiple classes (tranches) of securities with different priority claims on cash flows:
- **Senior Tranche (AAA-rated, ~80–90% of deal):** First claim on all interest and principal payments. Protected from losses until subordinate tranches are wiped out. Receives lowest interest rate.
- **Mezzanine Tranche (BBB/BB-rated, ~5–10%):** Absorbs losses after equity is exhausted. Higher interest rate than senior.
- **Equity/First-Loss Tranche (~5–10%):** Also called "first loss" or "residual" tranche. Absorbs first losses. Often retained by originator (skin in the game). Highest expected return if losses are low; zero recovery if losses exceed its cushion.

**4. Credit Enhancement:** Mechanisms to improve senior tranche ratings:
- **Overcollateralization (OC):** Issuing securities worth less than the collateral pool value. If pool = $110M and notes issued = $100M, there's $10M excess cushion.
- **Excess Spread:** If mortgages pay 6% interest and the senior notes pay 4%, the 2% excess spread absorbs losses before principal.
- **Cash Reserve Account:** Cash deposited at deal inception to cover shortfalls.
- **Monoline Insurance / Credit Wraps (pre-2008):** Third-party guarantees from companies like AMBAC, MBIA — these proved catastrophic when the insurers themselves became insolvent.

**5. Servicer and Trustee:** The servicer collects payments from borrowers and forwards them to the SPV. The trustee represents investors' interests and monitors compliance with deal covenants.

---

### Quantitative Waterfall Structure

Consider a simplified $1 billion mortgage pool:

```
Collateral Pool: $1,000M mortgages (avg. coupon 6.5%)

Tranche Structure:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Senior AAA:    $800M  ←  Pays SOFR + 40bps  (~4.9% at SOFR=4.5%)
Mezzanine AA:  $60M   ←  Pays SOFR + 130bps (~5.8%)
Mezzanine A:   $40M   ←  Pays SOFR + 230bps (~6.8%)
Mezzanine BBB: $30M   ←  Pays SOFR + 400bps (~8.5%)
Equity:        $70M   ←  Receives residual (target IRR: 12–18%)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Excess spread: 6.5% - weighted cost of notes ≈ ~1.3% → $13M/year cushion

Break-even loss rates:
  Equity tranche wiped at: 7% cumulative losses
  BBB tranche wiped at: 10% cumulative losses
  AA tranche wiped at: 13% cumulative losses
  AAA tranche wiped at: 21% cumulative losses
```

In a benign scenario (2% losses), all tranches receive full payment. In a stress scenario (15% losses), equity and all mezz tranches are wiped out, and mezzanine AA investors lose ~40% of principal. Senior AAA investors remain protected. This logic — and its failure in 2007–2008 — defines securitization's systemic importance.

---

### Asset Classes in Securitization

**Mortgage-Backed Securities (MBS):**
- **Agency MBS:** Pools of conforming mortgages guaranteed by Fannie Mae (FNMA), Freddie Mac (FHLMC), or Ginnie Mae (GNMA). Carry implicit or explicit US government guarantee. The ~$9 trillion agency MBS market is the world's most liquid fixed-income market after US Treasuries. The Federal Reserve held $2.7 trillion in MBS at peak (2022) as part of QE.
- **Non-Agency MBS:** Jumbo mortgages, subprime, Alt-A. No government guarantee. Market collapsed in 2008 and only partially recovered. Outstanding ~$800 billion in 2025.
- **CMBS (Commercial MBS):** Backed by commercial real estate — office buildings, hotels, retail centers. ~$950 billion outstanding. Under severe stress in 2023–2025 due to office market collapse (remote work) and retail distress.

**Asset-Backed Securities (ABS):**
- **Auto ABS:** Backed by auto loans or leases. ~$300 billion outstanding. Delinquency rates rose in 2023–2025 as subprime auto borrowers were squeezed. GM Financial, Ford Credit, Ally Financial are major issuers.
- **Credit Card ABS:** Backed by credit card receivables. ~$155 billion. Key risk: "early amortization" triggers if credit card delinquencies exceed threshold.
- **Student Loan ABS:** Backed by private student loans. ~$180 billion. Federal FFELP loans largely wound down under Obama-era policy changes.
- **CLOs (Collateralized Loan Obligations):** Perhaps the most important structured product in 2025–2026. Backed by leveraged loans (below-investment-grade corporate loans). US CLO market: ~$1.3 trillion. Global: ~$1.9 trillion. Fund approximately 65% of all leveraged lending. Managed by CLO managers (Blackstone, KKR, PGIM) who actively trade the loan pool within constraints.

---

### CDOs and the 2008 Crisis: A Case Study in Systemic Risk

The 2008 Global Financial Crisis was, at its core, a structured credit crisis driven by CDOs (Collateralized Debt Obligations) and CDO-squared vehicles.

**The CDO Innovation:** Starting in the mid-1990s, investment banks (Salomon Brothers, Bear Stearns, Merrill Lynch) began creating CDOs backed not by loans but by the mezzanine tranches of other securitizations — particularly subprime mortgage ABS. The logic: by diversifying across many mezzanine tranches, the new CDO senior tranche could be rated AAA even if each underlying component was rated BBB.

**The Flaw — Correlation Assumption:** CDO ratings models (primarily by Moody's, S&P, and Fitch) relied on the Gaussian Copula model (David Li, 2000) to estimate default correlations between underlying loans. The model assumed correlations based on historical data from a benign period. When house prices fell nationally for the first time since the Great Depression, correlations spiked toward 1.0 — all mortgages defaulted simultaneously — invalidating the diversification premise.

**CDO-Squared:** CDOs backed by the mezzanine tranches of other CDOs. Each layer of re-tranching added complexity and obscured the underlying risk. By 2007, a single CDO-squared could have exposure to the same underlying pool of subprime mortgages through dozens of intermediate structures.

**Timeline of Crisis:**
- **2004–2006:** Subprime mortgage origination surged. NINJA loans (No Income, No Job, No Assets). Lenders sold immediately to securitizers, eliminating any incentive for underwriting discipline.
- **February 2007:** HSBC announces $10.5B loss provision on US subprime mortgages.
- **June 2007:** Bear Stearns hedge funds (heavily levered in subprime CDOs) collapse. Mark-to-market losses revealed the model was broken.
- **August 2007:** BNP Paribas freezes three funds — the first European institution to acknowledge subprime exposure. Interbank lending freezes (LIBOR-OIS spread spikes from ~10bps to 90bps).
- **September 2008:** Lehman Brothers bankruptcy. AIG bailout ($182 billion) — AIG had written CDS protection on $500B+ of CDO tranches without sufficient capital.
- **Final Losses:** US residential mortgage-related losses exceeded $2 trillion. Global bank write-downs: ~$800 billion. The IMF estimated total financial sector losses at $4.1 trillion (2009 estimate).

**The ABX Index:** The ABX.HE indices, created in January 2006, tracked the credit default swap spreads on subprime MBS tranches. The ABX BBB- tranche fell from par (100) to below 10 cents by early 2009 — the canary in the coal mine that sophisticated investors (John Paulson, Michael Burry's Scion Capital, Goldman Sachs) used to bet against the housing market. John Paulson's profit: ~$15 billion in 2007 alone.

---

### CLOs in the Modern Market: Architecture and Risk

**Why CLOs Differ from Pre-Crisis CDOs:**
1. **No re-securitization:** CLOs are backed by actual corporate loans, not tranches of other structures
2. **Active management:** CLO managers can trade within constraints (limits on CCC-rated loans, single-obligor concentrations, weighted average spread tests)
3. **Risk retention:** Post-Dodd-Frank, US CLO managers must retain 5% of deal value ("skin in the game")
4. **Shorter-duration loans:** Leveraged loans are typically floating-rate (SOFR + spread), with 5–7 year terms

**CLO Waterfall Tests:**
CLOs contain "overcollateralization (OC) tests" and "interest coverage (IC) tests" that, if breached, redirect cash flows from junior tranches to pay down senior tranches or reinvest in safer assets:

```
Example OC Test (BBB tranche):
  Required: (Par Value of Loans) / (Outstanding BBB+ Liabilities) ≥ 125%
  If breached: Cash diverted from equity/mezz distributions to pay down AAA and AA notes
```

This self-healing mechanism makes CLOs structurally more resilient than pre-crisis CDOs.

**Performance During COVID-19 (2020):** Despite unprecedented corporate loan defaults, the AAA tranche of broadly syndicated CLOs experienced zero realized losses. CCC-rated bucket breaches were common, triggering OC test failures in many deals and redirecting cash to senior notes, but the structural protections worked as designed. This vindicated the post-crisis reform framework.

---

### Post-Crisis Regulatory Framework

**Dodd-Frank Act (2010, US):**
- Risk retention: Securitizers must retain at least 5% of credit risk (prevents "originate to distribute" abuses)
- Volcker Rule: Prohibited banks from sponsoring CLOs holding bonds (later amended to allow bond-free CLOs)
- Expanded SEC registration and disclosure requirements for ABS

**EU Securitization Regulation (2018):**
- Simple, Transparent, and Standardised (STS) label for compliant deals
- Risk retention: 5% minimum, same as US
- Due diligence requirements for investors
- Revised in 2022 to encourage "on-balance sheet" synthetic securitizations

**Basel III / IV Capital Requirements:**
- Banks using the Securitization External Ratings-Based Approach (SEC-ERBA) now face higher capital charges than under Basel II
- Output floor (Basel IV, phasing in 2025–2028) limits capital benefit from internal models
- Net effect: banks hold less securitization on balance sheet; pension funds and insurance companies are the marginal buyers

---

### Synthetic Securitization and Credit Default Swaps

Not all securitization involves actual asset transfers. In synthetic securitization:
- The protection seller (investor) takes credit risk exposure via CDS without owning underlying loans
- The protection buyer (bank) gets balance sheet relief without selling assets
- CLNs (Credit-Linked Notes) are bonds that embed a CDS — investors receive higher yield but bear credit losses on reference entities

**Synthetic Balance Sheet CDOs:** Major banks (Deutsche Bank, JPMorgan, Citigroup) created these in the late 1990s to manage regulatory capital. By selling credit protection on a reference portfolio via CDS to an SPV, the bank reduced Basel I capital requirements without selling the loans. This was the origin of the synthetic CDO market.

**Significant Risk Transfer (SRT) Transactions:** Post-2010, European banks increasingly use synthetic securitization for regulatory capital management. In an SRT deal, the bank buys protection on the 5–12% "mezzanine" risk slice of a loan portfolio. This reduces risk-weighted assets, freeing capital for lending. The ECB and PRA have scrutinized SRT transactions to ensure genuine risk transfer rather than regulatory arbitrage.

---

### Case Study: Countrywide Financial and the MBS Machine

Countrywide Financial, once the largest US mortgage lender, epitomizes the excesses of the securitization era:

- **2003–2006:** Originated ~$500 billion in mortgages annually (20% of US market)
- **Business model:** Originate → Securitize → Earn fees → Repeat. Almost no mortgages held on balance sheet beyond 30 days
- **FICO score drift:** Average FICO score of securitized loans fell from 700 (2001) to 640 (2006). Stated-income ("liar") loans grew to 40%+ of production
- **2008:** Acquired by Bank of America for $4 billion (after a $2.5B preferred investment by Warren Buffett in August 2007). BofA's total Countrywide-related losses eventually exceeded $50 billion
- **Settlement:** In 2013, BofA paid $8.5 billion to settle MBS investor claims; in 2014, $16.65 billion in the largest DOJ settlement in history

---

### The CLO Market in 2025–2026: Current Conditions

- US CLO issuance: ~$360 billion in 2024 (record year), tracking similar in 2025
- Spreads: AAA CLO spreads compressed to ~135bps over SOFR by mid-2025 (tightest since 2021), reflecting strong demand from Japanese banks and insurance companies
- Japan's megabanks (MUFG, SMBC, Mizuho) hold approximately 15% of global AAA CLO tranches — a significant concentration risk if Japanese institutions face capital pressures
- Private credit overlap: The rise of direct lending has created tension with the BSL (broadly syndicated loan) CLO market, as large buyouts increasingly bypass public loan markets
- "Refi/reset" wave: Low-spread CLOs from 2020–2021 are being called and reissued at wider spreads, generating significant CLO manager activity

---

### Cross-Disciplinary Connections

- **[[credit-markets-and-credit-risk]]:** Securitization is the primary mechanism for distributing credit risk across the financial system
- **[[derivatives-futures-and-forwards]]:** CDS are the derivatives backbone of synthetic securitization
- **[[behavioral-finance-and-investor-psychology]]:** The AAA rating heuristic and the "this time is different" narrative drove investor complacency pre-2008
- **[[macroeconomics-101]]:** MBS markets directly connect monetary policy (Fed MBS purchases) to mortgage rates and housing
- **[[hedge-funds-and-alternative-strategies]]:** CLO equity and mezzanine tranches are core alternative credit strategies

## Related
- [[credit-markets-and-credit-risk]]
- [[fixed-income-deep-dive]]
- [[derivatives-futures-and-forwards]]
- [[hedge-funds-and-alternative-strategies]]
- [[macroeconomics-101]]
- [[behavioral-finance-and-investor-psychology]]
- [[private-equity-and-venture-capital]]
- [[yield-curve-and-bonds]]


### Saturday Cross-Disciplinary Synthesis: Securitization as Information Engineering

**Connection to Information Theory — Credit Rating as Information Compression:**  
The central function of securitization — pooling heterogeneous loans into tranches with different risk profiles — is an information engineering exercise. Rating agencies (Moody's, S&P, Fitch) perform information compression: they replace the multivariate risk profile of thousands of individual loans with a single letter grade, dramatically reducing the information burden on investors. This information compression is exactly what the rating crisis of 2008 revealed as inadequate: by compressing complex mortgage pool risk into AAA-BBB-BB tranches, the rating agencies destroyed the distributional information that investors needed to understand tail risk. The CDO-squared and CDO-cubed structures — collateral pools composed of tranches from other CDO pools — were compound information compressions where the underlying correlations (default correlation between pooled assets) were completely obscured from the final investor. Shannon's channel capacity theorem is illuminating here: the rating system had a severely limited information bandwidth, which worked adequately for simple bond risk but catastrophically failed for the high-dimensional correlated risk of structured products.

**Connection to Sociology and Regulatory Capture:**  
The 2008 securitization crisis cannot be explained by information asymmetry alone — it also requires sociology of organizations and regulatory capture theory. The rating agencies' conflict of interest (issuers pay for ratings) is a classic Stiglerian regulatory capture (1971): the regulated entities (banks and structured finance issuers) captured the rating function to produce favorable outcomes. Sociologically, the SEC's 2000–2007 failure to regulate rating agencies' conflicts was facilitated by revolving-door dynamics (SEC examiners and rating agency analysts shared career pathways) and the "social license" granted to rating agencies as neutral technical arbiters. MacKenzie (2011) argues that CDO pricing models were not merely reflections of credit risk but performative — their widespread adoption by rating agencies and investors created the market conditions (high-correlation trading behavior) that made the models' assumptions self-defeating.

**Connection to AI and Machine Learning — ML for Structured Product Pricing:**  
Post-2008 securitization is increasingly using machine learning to address the information compression failures that ratings alone cannot prevent. NLP models applied to loan origination documents (mortgage applications, consumer credit agreements) can identify subtle language patterns that predict default risk beyond the numerical risk scores captured in traditional underwriting criteria. Gradient boosting models for CLO tranche pricing incorporate loan-level alternative data (employment history patterns, geographic exposure to climate risk, industry sector exposures) that structured rating approaches systematically miss. The democratization of ML-based loan analytics is enabling institutional investors to conduct independent risk analysis — reducing reliance on issuer-paid ratings and creating a more informationally efficient securitization market than existed pre-2008.

**Updated Related Connections:**  
- [[Tech & AI/machine-learning-fundamentals]] — ML for loan default prediction; gradient boosting for structured product pricing  
- [[Tech & AI/retrieval-augmented-generation]] — RAG for loan documentation analysis; contract review in securitization due diligence  
- [[Psychology/social-psychology-and-group-dynamics]] — Herd behavior in structured product markets; social proof in rating-dependent investing  
- [[World Events/2026-05-30-global-economic-outlook-2026-slowdown]] — CLO market stress in 2026 economic slowdown; leveraged loan default cycle

### Private Credit Securitization: The Next Frontier

The private credit boom of 2015–2026 has collided with the securitization market to create a new and rapidly growing frontier: **private credit securitization** — packaging illiquid direct loans into tradeable structured products, giving private credit managers access to the CLO market's term funding advantages.

**Why private credit managers securitize:**

Traditional private credit funds raise LP capital with a 5–10 year fund life. This creates a maturity mismatch: loans may mature at year 3, requiring reinvestment decisions, while the fund life runs to year 10. Securitization via CLO structures provides:

1. **Permanent or long-dated capital:** CLO liabilities (AAA notes) can be issued for 12+ year maturities, matching the 5–7 year loan terms
2. **Non-recourse leverage:** The CLO liability structure allows leverage of 2–4× on the underlying loans without recourse to the manager or upstream fund vehicles
3. **Cost of capital reduction:** AAA CLO note spreads (SOFR + 130–160bps in 2026) are lower than LP equity return expectations (~12–15%), dramatically reducing the blended cost of capital

**The "Middle Market CLO" structure:**

Unlike broadly syndicated loan (BSL) CLOs backed by liquid, rated loans, middle market CLOs (MM CLOs) are backed by private direct loans:

```
Structural differences between BSL and MM CLOs:

BSL CLO (standard):            MM CLO (private credit):
Loan pool: 200–300 loans       Loan pool: 30–75 loans (less diversified)
Loan size: $50M–$500M avg      Loan size: $5M–$50M avg
SOFR+ spread: 300–400bps       SOFR+ spread: 500–700bps
Loan ratings: mostly B/B+      Loans: unrated (credit assess by manager)
Manager discretion: medium     Manager discretion: high (fully managed)
AAA note spread: SOFR+130bps  AAA note spread: SOFR+165–200bps (wider for less liquid pool)
Equity tranche yield: 15–20%   Equity tranche yield: 18–25% (higher risk premium)
```

**The Business Development Company (BDC) structure:**

BDCs are publicly traded investment vehicles (similar to REITs in structure) that invest in middle market direct loans. They can use leverage up to 1:1 debt-to-equity (recently increased from 1:1 to 2:1 under the Small Business Credit Availability Act, 2018). Major BDCs — Ares Capital (ARCC, ~$22B assets), Golub Capital BDC (GBDC), Blue Owl Capital (OBDC) — are effectively the retail-accessible version of direct lending.

**The synthetic securitization of private credit:**

Insurance companies (particularly those owned by PE firms: Athene by Apollo, Global Atlantic by KKR, F&G by Fidelity National Financial, backed by Blackstone) use Significant Risk Transfer (SRT) synthetic securitization to optimize their capital structure:

```
Insurance company balance sheet:
  Fixed income assets: $100B
  Technical reserves (liabilities): $90B
  Capital base: $10B

SRT transaction:
  Reference portfolio: $10B of middle-market loans
  Insurance company buys protection on first-loss (0–5%) and mezzanine (5–12%)
  "Super senior" tranche (12–100%) retains the insurance company
  Structured note issued to third-party investors on the protected tranches

Capital relief: Reduces RBC (Risk-Based Capital) requirements by ~$800M
  on the $10B portfolio (from ~$1.2B to ~$400M)
Returns freed capital for additional premium writing or investments
```

This creates a feedback loop: PE firms own insurance companies, use them to fund PE portfolio companies through direct lending, then securitize those loans via SRT to free regulatory capital for more premium writing and investments — a closed ecosystem of capital recycling that regulators (NAIC, state insurance commissioners) are actively scrutinizing in 2026.

**2026 CLO Market Dynamics:**

The US CLO market experienced record issuance in 2025 (~$360B) driven by three factors:
1. Spread tightening: AAA CLO spreads compressed to SOFR+130bps (tight end of range), incentivizing refinancing/reset activity
2. Record leveraged buyout activity drove record leveraged loan issuance, providing feedstock for new CLOs
3. Japanese bank demand: MUFG, SMBC, and Mizuho collectively purchased ~$35–45B in AAA CLO notes in 2025, attracted by yen-hedged yields of approximately 5.2% vs. JGB yields of 1.5%

The concentration of Japanese bank demand is flagged by regulators as a systemic risk: if BOJ normalization forces Japanese banks to repatriate capital (as occurred partially in August 2024), the resulting AAA CLO selling pressure could drive spreads significantly wider, triggering repricing across the leveraged credit spectrum.


---

### June 10, 2026 Addition: CLO Market Dynamics in 2026 and the Commercial Real Estate CDO Reckoning

**CLO market in 2026: $220B in new issuance projected.** The US CLO market is on pace to issue approximately $220 billion in new formation in 2026 — nearly matching the record $220B of 2021. The driver: sustained spread-compression in investment-grade CLO tranches (AAA CLO spreads tightened to approximately SOFR+135 bps in early June 2026, the tightest since pre-financial-crisis levels), creating attractive arbitrage between leveraged loan spreads (averaging SOFR+380 bps) and CLO liability costs. The CLO equity tranche — the junior-most piece that absorbs first losses — is the primary beneficiary of the tight liability environment: with total financing cost (weighted average of all CLO liabilities) averaging approximately SOFR+195 bps vs. collateral yielding SOFR+380 bps, the equity excess spread of ~185 bps provides substantial cushion before defaults impair equity cash flows.

**The Japanese CLO demand concentration risk.** Japanese regional banks and insurance companies have become the dominant buyers of AAA and AA-rated CLO tranches, accounting for approximately 35–40% of primary market demand (up from 15% in 2018). The motivation: CLO AAA tranches offer SOFR+135 bps in June 2026, while JGB 10-year yields are only 1.0–1.1%. Even after currency hedging costs (~120 bps for USD/JPY), Japanese investors earn approximately +15 bps pickup — modest but better than domestic alternatives. The systemic concern: if BOJ normalization accelerates (Japanese rates rising faster than expected), the currency hedging cost increases and erodes the pickup, potentially triggering Japanese CLO demand withdrawal. Estimated Japanese CLO holdings: approximately $300 billion across all ratings. A rapid Japanese exit would not cause defaults but would widen spreads sharply across the entire CLO capital structure.

**The CMBS (Commercial Mortgage-Backed Securities) reckoning.** While CLOs are performing well, the commercial mortgage-backed securities (CMBS) market is facing a structural challenge that represents the most significant securitization stress since 2008. The specific problem: CMBS issued in 2018–2022, backed by office, retail, and mixed-use commercial real estate, is coming due for refinancing in 2024–2027. The combination of (1) post-COVID permanent structural demand reduction for office space (work-from-home reducing office utilization to 55–65% of pre-COVID levels in major US cities), (2) higher refinancing rates (CMBS loans taken at 3–4% in 2019–2021 must refinance at 6–8% today), and (3) declining NOI (net operating income) for office properties as lease expirations are not renewed creates a "triple squeeze" on office property valuations.

**CMBS delinquency data (June 2026).** Trepp's CMBS delinquency tracker shows: office CMBS 30+ day delinquency rate at 8.7% (June 2026), up from 1.2% in January 2022. Retail CMBS: 6.3% (recovering from 10%+ COVID peak). Lodging CMBS: 4.1% (post-recovery normalization). Industrial/multifamily CMBS: 0.8% (structural strength). The office delinquency rate of 8.7% is the highest in CMBS history for office, though significantly below the CDO defaults of 2008–2009. Several major office property complex defaults are in workout: a $1.5 billion CMBS pool backed by San Francisco office properties (Brookfield default), a $750 million pool for Manhattan Class B office (WeWork-leased space), and several regional mall CMBS structures in legacy retail markets.

**Historical analogy: the RTC (Resolution Trust Corporation) playbook.** The 1989–1995 savings and loan crisis required the US government's Resolution Trust Corporation to take over and liquidate failed thrift institutions holding $400 billion in commercial real estate assets. The RTC process took 6 years and resulted in ~40% average recovery on commercial real estate portfolios. The 2024–2027 office CMBS reckoning is not a systemic crisis on that scale — the losses are dispersed across institutional investors (not concentrated in insured depository institutions) and the amounts are smaller in GDP terms (~$180 billion in at-risk CMBS vs. $400 billion in RTC assets). But the workout process — extending loans, converting debt to equity, selling properties at distressed prices — will play out over 4–6 years, creating both risk and opportunity in the CMBS secondary market.
