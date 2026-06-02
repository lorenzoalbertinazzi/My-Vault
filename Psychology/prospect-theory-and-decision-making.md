---
title: Prospect Theory and Decision-Making Under Uncertainty
date: 2026-05-30
tags: [psychology, behavioral-economics, prospect-theory, kahneman, tversky, decision-making, loss-aversion, cognitive-biases, reference-dependence, value-function, probability-weighting, endowment-effect, status-quo-bias, disposition-effect, framing-effects]
source: "Kahneman & Tversky (1979) Prospect Theory: An Analysis of Decision Under Risk, Econometrica; Tversky & Kahneman (1992) Advances in Prospect Theory: Cumulative Representation of Uncertainty, J Risk Uncertainty; Thaler (1980) Toward a Positive Theory of Consumer Choice, JEconBehav; Kahneman (2011) Thinking, Fast and Slow"
last_updated: 2026-06-02
---

## Summary
Prospect Theory, developed by Daniel Kahneman and Amos Tversky in their landmark 1979 paper in *Econometrica*, is arguably the most important contribution to behavioral economics and decision science of the 20th century. It provides an empirically grounded alternative to Expected Utility Theory (EUT) — the dominant normative framework for rational decision-making under uncertainty — by showing systematically how people actually make decisions. The theory introduces four core principles: (1) people evaluate outcomes as **gains or losses relative to a reference point**, not as absolute wealth states; (2) the value function is **concave for gains and convex for losses** (diminishing sensitivity); (3) **losses loom larger than equivalent gains** (loss aversion, λ ≈ 2.25); and (4) people systematically **overweight small probabilities** and underweight large ones. These four principles explain an enormous range of human behavior — from insurance purchase to stock market anomalies to negotiation strategy — and have reshaped economics, finance, public policy, and medicine since the 1970s.

## Key Points
- Kahneman won the Nobel Memorial Prize in Economics (2002); Tversky died in 1996 before the Nobel could be awarded jointly
- The 1979 paper is the most-cited paper in economics; its follow-up **Cumulative Prospect Theory** (1992) resolved early mathematical issues
- **Loss aversion coefficient (λ):** Empirically estimated at ~2.25 — a $100 loss requires ~$225 in gains to compensate psychologically
- **The value function:** v(x) = x^α for gains, v(x) = −λ(−x)^β for losses; typically α = β ≈ 0.88
- **Probability weighting function:** w(p) = p^γ / [p^γ + (1−p)^γ]^(1/γ); γ ≈ 0.65 for gains, 0.69 for losses
- The **Fourfold Pattern**: risk-averse for high-probability gains, risk-seeking for high-probability losses, risk-seeking for low-probability gains (lottery), risk-averse for low-probability losses (insurance)
- Prospect Theory directly explains: the **disposition effect** in stock markets, the **equity premium puzzle**, the **endowment effect**, **status quo bias**, and **sunk cost fallacy**

## Details

### 1. Historical and Intellectual Context

**The Dominant Orthodoxy: Expected Utility Theory**
For two centuries following Daniel Bernoulli's 1738 paper *Specimen Theoriae Novae de Mensura Sortis* (which introduced the concept of diminishing marginal utility to explain the St. Petersburg Paradox), economists worked within the Expected Utility Theory framework. EUT, formally axiomatized by von Neumann and Morgenstern in *Theory of Games and Economic Behavior* (1944) and extended by Savage's subjective probability framework (1954), states that rational agents maximize the expected utility of outcomes:

```
EU = Σ p_i × u(w_i)
```

Where p_i is the probability of outcome i and u(w_i) is the utility of the resulting wealth level w_i.

The von Neumann–Morgenstern axioms — completeness, transitivity, continuity, and independence — define rational preference. Under these axioms, agents evaluate lotteries based on final wealth levels, have consistent probability beliefs, and satisfy the substitution (independence) axiom.

**Early Anomalies:**
Even before Kahneman and Tversky, evidence mounted against EUT:
- **Allais Paradox (1953):** Maurice Allais demonstrated that when given specific lottery choices, virtually all respondents violate the independence axiom. Most people prefer a certain $1M over a gamble with 89% chance of $1M, 10% chance of $5M, 1% chance of $0 — yet by substitution this implies preferences inconsistent with EUT.
- **Markowitz (1952):** Pointed out that the same person buys insurance and lottery tickets — risk-averse in one domain, risk-seeking in another — suggesting the utility function changes shape around a reference point.

**The Kahneman-Tversky Program:**
Daniel Kahneman (cognitive psychologist at Hebrew University of Jerusalem) and Amos Tversky (mathematical psychologist and decision theorist) began collaborating in the late 1960s. Their methodology was revolutionary: rather than theorizing about how people *should* decide, they ran **systematic experiments** to document how they *actually* decide.

Their 1974 *Science* paper "Judgment Under Uncertainty: Heuristics and Biases" catalogued cognitive shortcuts (representativeness, availability, anchoring) as systematic sources of error. The 1979 Prospect Theory paper synthesized their experimental findings into a formal model. Over the subsequent two decades, they and their intellectual descendants (Richard Thaler, Robert Shiller, George Akerlof) constructed the entire edifice of behavioral economics.

### 2. Core Principles of Prospect Theory

#### 2.1 Reference Dependence

People do not evaluate outcomes as absolute wealth levels; they evaluate them as **changes from a reference point** — which is typically the status quo but can also be an aspiration level, a social comparison, or a prior expectation.

**Experimental evidence:** Kahneman and Tversky presented subjects with two scenarios:
- Scenario A: You have $1,000 and must choose between (a) gaining $500 for certain or (b) 50% chance of gaining $1,000 and 50% of gaining $0
- Scenario B: You have $2,000 and must choose between (a) losing $500 for certain or (b) 50% chance of losing $1,000 and 50% of losing $0

Under EUT, Scenarios A and B are identical (both offer certain $1,500 vs. 50-50 between $1,000 and $2,000). But the empirical result was dramatically different: in Scenario A, ~85% preferred the certain gain (a); in Scenario B, ~70% preferred the gamble (b). The same final wealth levels produce opposite risk preferences depending on the frame.

#### 2.2 The Value Function

The value function v(x) maps gains and losses (deviations from the reference point) to subjective value:

```
v(x) = x^α           for gains (x ≥ 0)
v(x) = −λ(−x)^β      for losses (x < 0)
```

**Parameters (from empirical estimation):**
- α = β ≈ 0.88 (diminishing sensitivity in both domains)
- λ ≈ 2.25 (loss aversion coefficient)

**Shape properties:**
1. **Concave for gains** (diminishing marginal joy): The difference between $0 and $100 gain feels larger than between $900 and $1,000 gain. This reflects diminishing marginal utility of gains.
2. **Convex for losses** (diminishing marginal pain): The difference between $0 and $100 loss feels larger than between $900 and $1,000 loss. This produces risk-seeking behavior in the loss domain — people gamble to avoid accepting certain losses.
3. **Asymmetric slope at origin** (loss aversion): The value function is steeper for losses than gains. Losing $100 hurts approximately 2.25 times more than gaining $100 feels good.

**Worked illustration:**
A taxi driver who mentally accounts daily has a target income of $200/day. On a rainy day (where demand is high), hitting $200 quickly, he goes home early — stopping work when he's "up." On a slow day, he works extra hours trying to avoid finishing the day "down." This is reference-dependent utility: the reference is the daily target, not total wealth.

#### 2.3 Loss Aversion

Loss aversion (λ ≈ 2.25) is the most practically powerful element of Prospect Theory. It means:

```
v(−x) ≈ −2.25 × v(x)
```

**Implications:**
- A 50/50 gamble of ±$X is rejected unless the gain is roughly 2.25× the potential loss
- Marketing "free trials" and "no risk returns" work by eliminating the potential loss, making customers willing to engage
- Labor economics: wage cuts generate enormous morale destruction (far greater than equivalent wage increases generate satisfaction). This helps explain **nominal wage rigidity** — employers avoid cuts even at the cost of layoffs.
- Insurance: People overpay for insurance relative to actuarially fair prices because potential losses loom disproportionately large.

**The Endowment Effect (Thaler, 1980):**
Derived directly from loss aversion: once you own something, giving it up constitutes a loss (valued at 2.25x) while acquiring it was merely a foregone gain (valued at 1x). Therefore, the minimum price a seller demands exceeds the maximum price a buyer will pay for the identical object.

Classic experiment: half of students randomly assigned coffee mugs; buyers offered, on average, ~$2.87; sellers demanded, on average, ~$7.12 — a 2.5x gap. The mugs weren't worth more to sellers because they preferred them; they were worth more because giving them up felt like a loss.

#### 2.4 Probability Weighting

Prospect Theory replaces objective probabilities with a **probability weighting function** w(p):

```
w(p) = p^γ / [p^γ + (1−p)^γ]^(1/γ)
```

Empirically, γ ≈ 0.65 for gains, 0.69 for losses (Tversky & Kahneman 1992).

**Key properties:**
- w(0) = 0, w(1) = 1 (certainty effects preserved)
- **Overweighting small probabilities:** w(0.01) ≈ 0.057 (actual weight ~5.7% vs. true 1%)
- **Underweighting moderate-to-large probabilities:** w(0.50) ≈ 0.38, w(0.90) ≈ 0.71
- **Discontinuity at boundaries:** The jump from 0 to "possible" and from "almost certain" to "certain" both have disproportionate subjective impact (**certainty effect**)

**The Fourfold Pattern of Risk Attitudes (Tversky & Kahneman, 1992):**

| | Gains | Losses |
|---|-------|--------|
| **High Probability** | Risk Averse (prefer certain gain) | Risk Seeking (prefer gamble over certain loss) |
| **Low Probability** | Risk Seeking (lottery preference) | Risk Averse (purchase insurance) |

This elegant 2×2 table explains what appeared to be contradictory risk preferences: the same person buys lottery tickets (low-probability gain, overweighted) and insurance (low-probability loss, overweighted) while also preferring certain large gains over uncertain slightly larger gains and taking gambles to escape certain large losses.

### 3. Cumulative Prospect Theory (1992)

The original 1979 theory had a mathematical flaw: by overweighting small probabilities, it could assign higher value to stochastically dominated lotteries (violating first-order stochastic dominance). Tversky and Kahneman's 1992 paper in the *Journal of Risk and Uncertainty* resolved this with **Cumulative Prospect Theory (CPT)**, which applies probability weighting to the cumulative distribution function (CDF) rather than individual probabilities.

In CPT, the decision weight for outcome x_i depends on its rank in the ordered outcomes — the probability weighting is applied to the cumulative probability of outcomes at least as extreme, not to each outcome individually. This preserves stochastic dominance while retaining all empirical properties.

The CPT value function:
```
V(f) = Σᵢ₌₁ⁿ⁺ [w⁺(p₁+...+pᵢ) − w⁺(p₁+...+pᵢ₋₁)] × v(xᵢ)   (for gains)
     + Σᵢ₌₁ᵐ⁻ [w⁻(qᵢ+...+qₘ) − w⁻(qᵢ₊₁+...+qₘ)] × v(xᵢ)   (for losses)
```

CPT is now the standard version used in academic work, having largely superseded both EUT and original PT in empirical settings.

### 4. Real-World Applications

#### 4.1 Finance: The Equity Premium Puzzle
US equities have historically earned ~6% per year above Treasury bills. Under EUT, this implies implausibly high risk aversion (coefficient of relative risk aversion >30). Under prospect theory with loss aversion and narrow framing (mental accounting of annual or shorter-period returns), Benartzi and Thaler (1995) showed the equity premium is explained with myopic loss aversion — investors evaluate their portfolio annually and lose aversion to short-term volatility drives them to demand a large equity premium.

#### 4.2 Finance: The Disposition Effect
Investors sell winning stocks too early (take gains before they reverse) and hold losing stocks too long (avoid realizing losses). Shefrin and Statman (1985) coined this as the "disposition effect." Under CPT:
- Selling a winner "locks in" a gain in the concave gain region (less painful to sell)
- Selling a loser requires realizing a loss in the convex loss region — where the value function predicts risk-seeking, so investors prefer the gamble of holding

Empirical evidence (Odean 1998, using 10,000 brokerage accounts): Investors realize gains 68% more often than they realize losses. The stocks they hold (losers) subsequently underperform by ~3.4% annually vs. stocks they sell (winners) — demonstrating the irrationality.

#### 4.3 Marketing and Consumer Behavior
**Framing effects are directly derived from reference points:**
- "Save $50" vs. "Avoid a $50 surcharge" — the latter triggers loss aversion and produces higher behavioral response
- Free shipping ($0 shipping vs. $4.95 product discount): The certain avoidance of the $4.95 "loss" of shipping costs is more motivating than equivalent cash discount
- "Trial periods" remove the purchase decision from the loss domain; customers evaluate ending the trial as a loss of the service they've already integrated

Amazon Prime's free trial → paid subscription conversion leverages the endowment effect and loss aversion: canceling feels like giving up something already owned.

#### 4.4 Public Policy: "Nudge" Theory (Thaler and Sunstein)
The 2008 book *Nudge* systematized how policymakers can redesign choice architectures to exploit prospect theory:
- **Default enrollment** in pension plans (opt-out vs. opt-in): Switching from opt-in to opt-out increased UK pension participation from 65% to 90%+, because inaction (the default) is reference point; departing from it feels like a loss
- **Loss-framing communications:** Tax compliance letters framed as "you are currently not paying your fair share" outperformed standard letters by 5% response rate in HMRC experiments
- The UK Behavioural Insights Team ("Nudge Unit") reported saving £300M+ in tax revenues in its first year through prospect-theory-informed letter framing

#### 4.5 Medicine
- **Treatment framing:** A cancer surgery described as "90% survival rate" leads to higher acceptance than "10% mortality rate" — framing around the loss domain activates greater risk aversion
- **Medication adherence:** Loss-framed incentives ($100 forfeited if pills missed vs. $100 earned if pills taken) produce 30% better adherence in hypertension management trials

#### 4.6 Sports
**The "par as reference point" in golf (Pope and Schweitzer, 2011):** Analyzing 2.5M PGA Tour shots, putts that, if made, save par (avoiding a bogey = avoiding a loss) are made 2–3% more often than birdie putts of identical difficulty, difficulty measured by distance and positioning. Professional golfers — among the most skilled, incentivized decision-makers in any domain — exhibit loss aversion in real-time. The stakes: approximately $1.2M in total earnings differentials attributable to this effect over a career.

### 5. Criticisms and Competing Theories

**Critique 1: Reference point determination is underspecified.** Where exactly is the reference point? Different reference points (status quo, expectations, social comparisons, historical peak) generate different predictions. The theory offers no universal rule. Extensions attempt to address this: K sőszegi and Rabin's (2006) "expectation-based" reference points derive reference points from rational expectations in equilibrium.

**Critique 2: Ecological rationality (Gerd Gigerenzen).** Gigerenzen argues that heuristics produce efficient decisions in natural environments where they evolved; laboratory demonstrations of biases don't imply real-world irrationality. In natural frequency formats (1 in 10) rather than probability formats (10%), many "biases" disappear. He argues PT describes lab behavior, not real decision-making.

**Critique 3: Regret Theory (Bell 1982; Loomes & Sugden 1982).** Proposes that people anticipate regret and relief when comparing actual outcomes to foregone alternatives — a different mechanism than loss aversion that generates similar predictions in some domains but different ones in others.

**Critique 4: Context dependence.** The parameters (λ, α, β, γ) estimated from Western, educated, industrialized, rich, democratic (WEIRD) samples may not generalize universally. Cross-cultural studies show variation in loss aversion coefficients (Chinese subjects: λ ≈ 2.0; Japanese: λ ≈ 2.5; Nigerian: λ ≈ 1.8).

**Critique 5: Inability to explain long-run behavior.** Prospect Theory is most powerful in one-shot decisions. In repeated games with learning, subjects often converge toward EUT predictions. The theory's applicability to long-horizon decisions (retirement savings, career choices) is debated.

### 5a. Neuroeconomics: Neural Correlates of the Value Function

Behavioral experiments established the shape of the value function empirically; neuroeconomics has begun identifying the specific brain regions implementing it.

**Prospect Theory in the Brain (Tali Sharot, Robb Rutledge, et al.):**

**Gains and Losses — Asymmetric Processing:**
- **Gains:** Activate the ventral striatum (nucleus accumbens), particularly the caudate nucleus; this activation scales with the magnitude of the gain and correlates with self-reported positive affect
- **Losses:** Activate the **amygdala** and **anterior insula** more strongly and more rapidly than equivalent gains — the neural substrate of loss aversion; amygdala activation to losses peaks ~200ms post-stimulus vs. ~400ms for gains, consistent with an automatic alarm system
- **The ratio:** Amygdala BOLD signal for a $20 loss is approximately 2–3× larger than for a $20 gain (Tom et al. 2007, *Science*, N=16 fMRI): directly observed neural loss aversion ratio of ~2–3, consistent with the behavioral estimate of λ ≈ 2.25

**Reference Point Encoding:**
- The striatum and orbitofrontal cortex (OFC) track reference points dynamically; neurons in the OFC encode value *relative to context* (a $10 reward after $1 expectations activates differently than after $100 expectations)
- Monkey electrophysiology (Padoa-Schioppa & Assad 2006, *Nature*): OFC neurons encode the subjective value of options with a concave function for gains, consistent with diminishing sensitivity

**Probability Weighting — Neural Evidence:**
- The **anterior insula** overrepresents small probabilities: its activation to a 1% chance of loss exceeds what a linear probability model would predict — directly imaging the probability overweighting function
- The **dorsal striatum** tracks expected value in a more EUT-consistent manner; the interplay between striatum and insula may determine whether behavior follows PT or EUT in a given situation

**Lesion Studies:**
- Patients with **ventromedial prefrontal cortex (vmPFC) damage** show reduced loss aversion — they accept 50/50 gambles at near-EV rates even when loss aversion should cause refusal. The vmPFC appears necessary for implementing the affective component of loss aversion. These patients appear to decide "more rationally" on standard PT problems while making worse real-life decisions (the Somatic Marker Hypothesis, Damasio 1994)
- **Insula lesion patients** show reduced probability overweighting — suggesting the insula implements the probability distortion component of PT

**Key Implication:** Loss aversion is not merely a cognitive bias but an emotional response implemented by ancient threat-detection systems (amygdala) that evolved to prioritize danger avoidance. Rational training can dampen but not eliminate it; structural interventions (removing loss frames, making gains salient, changing choice architecture) are more effective than willpower.

### 5b. Cross-Cultural Evidence and Variations

**How Universal Is Loss Aversion?**
The loss aversion coefficient λ shows real but modest cross-cultural variation:

| Culture/Sample | λ (approximate) | Source |
|---|---|---|
| US (original K&T samples) | ~2.25 | Kahneman & Tversky 1979 |
| Western European samples | ~2.0–2.5 | Meta-analysis, Neumann & Böckler 2009 |
| Chinese samples | ~1.8–2.1 | Wang & Johnston 1995 |
| Japanese samples | ~2.3–2.7 | Wakker 2010 |
| Nigerian samples | ~1.5–1.8 | Birnbaum & Wakcher 2009 |
| Expert traders (professional) | ~1.3–1.6 | Haigh & List 2005 |

**Key Findings:**
1. Loss aversion appears in all studied cultures but with systematic variation — suggesting a universal bias with culturally modulated magnitude
2. **Professional expertise reduces loss aversion:** Experienced options traders and professional fund managers show significantly lower λ than the general population (Haigh & List 2005 with Chicago Board of Trade floor traders)
3. **Individualism-collectivism:** More collectivist cultures (Japan, China) show somewhat higher loss aversion in some studies, possibly because losses threaten group face; more individualist cultures show lower λ
4. **WEIRD samples (Western, Educated, Industrialized, Rich, Democratic):** Most original PT research used US or Israeli university students; the cross-cultural generalizability is supported but parameters differ

**The Expert Question:**
Do markets "educate away" loss aversion? Evidence is mixed:
- Floor traders: reduced loss aversion in standardized gambles (Haigh & List 2005)
- Institutional investors: still exhibit the disposition effect (Shapira & Venezia 2001)
- Interpretation: Expertise reduces loss aversion in the specific domain of expertise (risk trading) but doesn't generalize across domains — a domain-specific, not domain-general, correction

### 5c. Dynamic Reference Point Updating

The original PT treats reference points as fixed. Real decisions involve reference points that shift dynamically based on expectations, aspirations, and recent outcomes.

**Kőszegi-Rabin Expectations Model (2006, *Quarterly Journal of Economics*):**
The most influential theoretical extension: reference points are derived from **rational expectations** — what you expected to happen, not just the status quo. If you expected a $50,000 salary and received $45,000, the loss is $5,000 even if you earned more than last year.

**Predictions unique to K-R model:**
1. **Endowment effect without ownership:** If you pre-commit to a price, you won't accept less — even if you never physically possessed the item
2. **Expectation-based loss aversion in labor markets:** Workers experiencing an expected income receive different utility than workers experiencing the same income unexpectedly (one is in the gain region, one in the loss region, depending on prior expectations)

**Reference Point Adaptation:**
Research on hedonic adaptation shows that reference points shift over time:
- Lottery winners' well-being returns to pre-win baseline within 2–3 years (Brickman et al. 1978 — the hedonic treadmill)
- Paraplegics' life satisfaction recovers substantially within 8 weeks (contradicting the strong intuition that permanent loss destroys well-being permanently)
- Mechanism: The reference point adapts upward or downward toward the new reality, reducing the subjective loss/gain over time
- **Practical implication for wealth:** The decision to increase your standard of living (buy a bigger house, nicer car) shifts your reference point upward, requiring ever-larger incomes to remain in the "gain" domain

**The Aspiration Gap:**
Organizational behavior research (Cyert & March 1963, *Behavioral Theory of the Firm*): organizations use **aspiration levels** as reference points; performance above aspiration is satisfactory (risk-averse behavior follows), performance below aspiration triggers risk-seeking behavior (companies in distress take larger gambles). This is prospect theory applied to organizational strategy — and accurately predicts real corporate behavior in financial distress.

### 6. Prospect Theory and Negotiation

Loss aversion has profound implications for negotiation:
- **Anchoring:** The first number sets the reference point; all subsequent numbers are evaluated as gains or losses from the anchor. High initial offers shift the reference point to the negotiator's advantage.
- **Framing concessions:** "I'm giving you X" (gain frame for the counterparty) is less effective than "I'm giving up X" (loss frame for yourself, implying sacrifice) — the latter triggers reciprocity norms.
- **The BATNA (Best Alternative to a Negotiated Agreement):** Your BATNA defines your reference point. A party with a poor BATNA experiences the prospect of losing the deal as a "loss from agreement" — making them more risk-seeking and willing to accept worse terms rather than walk away.
- **Deadline effects:** As deadlines approach, the potential loss of a deal grows larger in psychological weight, inducing concessions — consistent with loss aversion over the reference point of "deal exists."

---

### Common Misconceptions

**Misconception 1: "Loss aversion means people always avoid risk."**
Loss aversion does not produce uniform risk-aversion — it produces *asymmetric* risk preferences that depend on whether the decision is framed in the gain or loss domain. The Fourfold Pattern (Tversky and Kahneman, 1992) demonstrates this clearly: in the loss domain with small probabilities (e.g., 5% chance of losing $200), people are *risk-seeking* — they prefer the gamble to the certain small loss, because the small certain loss feels worse than the gamble's expected value would suggest. This is why people buy lottery tickets despite negative expected value (small probability gain domain, risk-seeking) AND why people fail to cut investment losses quickly (moderate probability loss domain, risk-seeking to avoid crystallizing the loss). Understanding the fourfold pattern rather than the simplified "loss aversion = risk aversion" is essential for applying prospect theory correctly.

**Misconception 2: "Loss aversion is always irrational and should be overcome."**
Loss aversion evolved for good reasons: in environments with survival stakes, losses (of food, shelter, territory, allies) were often irreversible while equivalent gains were not. The asymmetry in the value function reflects a genuine asymmetry in the consequences of errors: a false positive (treating a gain as smaller than it is) is much more recoverable than a false negative (treating a loss as smaller than it is). In domains where losses are genuinely catastrophic and irreversible — personal security, health, concentrated financial risk — loss aversion is *calibratively appropriate*: it prevents taking risks whose downside is disproportionately severe. The problem arises when the evolved mechanism is applied in domains where the stakes are not survival-relevant (a small financial loss, a social embarrassment, a minor status setback) and the costs of loss aversion (missed opportunities, poor risk calibration) exceed the benefits of protection.

**Misconception 3: "Reference points are stable and obvious."**
Reference points are psychologically constructed, often ambiguous, and manipulable — which is precisely why they are so consequential for negotiation, marketing, and policy. A salary offer feels like a $5,000 gain if the reference point is your current salary and like a $5,000 loss if the reference point is what you expected to be offered. Multiple simultaneous reference points can apply (current salary, industry median, peer salaries, job offer prior) and may conflict. Skilled negotiators exploit reference point manipulation: the anchor sets a reference point that makes subsequent numbers feel like gains or losses relative to the anchor rather than to the true alternatives. The Kőszegi-Rabin model's most important insight is that expectations-based reference points are forward-looking (what did you expect?), not just backward-looking (what do you have?), making reference point determination a dynamic process rather than a fixed starting state.

**Misconception 4: "Prospect Theory is universally valid — it describes all human decision-making."**
The theory's empirical scope, while broad, is bounded by important conditions. Kahneman and Tversky developed the theory from choice problems involving explicitly stated probabilities — a format rare in everyday decision-making, where probabilities are estimated rather than given. Prospect Theory's predictions are most robust for: decisions with described (stated) probabilities, moderate-stakes decisions (not survival-level or trivially small stakes), and individual choice contexts. The predictions weaken for: experienced (repeated) decisions with learned probabilities (where people may converge toward more rational behavior through feedback), very high stakes (where deliberate analysis overrides heuristic processing), and group decisions (where social dynamics introduce considerations absent from the individual choice model). The WEIRD (Western, Educated, Industrialized, Rich, Democratic) population problem is also relevant: loss aversion magnitude varies across cultures (λ estimates range from ~1.5 in some East Asian samples to ~2.5 in US samples), suggesting the specific parameter values are not universal even if the qualitative pattern is.

---

### Worked Example: Investment Decision Under Prospect Theory

**Scenario**: You hold a stock (let's call it "MedTech") purchased at $100/share. Current price is $75/share. You're evaluating whether to hold or sell. Simultaneously, you're considering purchasing a second stock ("BioFund") with expected return of 15% and standard deviation of 25%.

**Step 1: Identify the reference point**
For the existing holding: your reference point is the purchase price, $100. Current position is in the loss domain: you are at $75, which is $25 below reference ($100). Reference point alternatives to consider:
- Your purchase price ($100) — most common psychological reference
- The stock's 52-week high ($110) — produces even larger perceived loss
- What you'd earn in an index fund over the same period (e.g., +8% → $108) — produces an opportunity cost loss frame
- Break-even ($75) — if you have psychologically "reset" to current price as reference

**Step 2: Apply the value function**
Under prospect theory's value function with α ≈ 0.88 and λ ≈ 2.25:
- Value of the $25 loss from reference: v(-25) = -λ × (25)^α = -2.25 × (25)^0.88 = -2.25 × 19.1 ≈ -43.0 utility units
- This is larger in absolute value than the utility of an equivalent $25 gain: v(+25) = (25)^0.88 ≈ 19.1 utility units

**Step 3: The disposition effect prediction**
Prospect Theory predicts you will hold the losing position too long (the "disposition effect"): selling at $75 crystallizes a $25 loss and produces v(-25) = -43.0 utility. Holding maintains the *hope* of returning to the reference point ($100) with a gamble that could eliminate the loss. The mathematical expression: if you believe there's a 40% chance MedTech returns to $100 and 60% chance it falls to $50, the expected value of holding is:

EV(hold) = 0.4 × v($0, gain relative to current) + 0.6 × v(-$25, further loss) 
≈ 0.4 × 0 + 0.6 × -43.0 = -25.8 utility units vs. selling for -43.0 utility units

This makes holding *feel* better despite the negative expected outcome — because the value function in the loss domain is concave (risk-seeking), the gamble's prospect theory value exceeds the certain loss's value even at a below-expected-value probability. This is the Prospect Theory mechanism behind portfolio managers holding losers for too long.

**Step 4: The rational response**
The rational response is to evaluate the decision based on current price ($75) as the *true* reference point, asking: "If I received $75 today and were deciding freshly whether to invest in MedTech, would I buy it at this price?" If yes, hold. If no, sell. The purchase price ($100) is a sunk cost that should not affect the forward-looking decision — but loss aversion makes it the psychological reference point regardless.

**Step 5: Applying this to BioFund**
For the new investment at current wealth, you are in the gain domain (considering a potential gain from current wealth). Prospect Theory predicts risk aversion in this domain: for an expected 15% gain with 25% standard deviation, you will subjectively discount the probability of positive returns. The corrective: explicitly calculate the expected value (EV = 15% × $invested) and compare it to your true risk tolerance at current wealth level — not to a reference point-inflated estimate.

## Related
- [[cognitive-biases]] — Prospect Theory provides the formal model mechanistically explaining loss aversion, framing, and anchoring biases
- [[negotiation-tactics]] — Reference points, anchoring, and loss framing are central to every negotiation outcome
- [[mental-models]] — Prospect Theory as the core decision-science mental model replacing expected utility
- [[habit-formation]] — Loss aversion (endowment effect) drives resistance to breaking bad habits and status quo behavior
- [[emotional-intelligence]] — Emotional regulation of loss aversion responses; amygdala activation during loss decisions
- [[48-laws-of-power]] — Scarcity and loss framing exploited throughout the 48 Laws as psychological leverage
- [[cialdini-influence]] — Scarcity principle and loss aversion are the neurobiological basis of Cialdini's scarcity trigger
- [[game-theory-and-strategic-thinking]] — Behavioral game theory; how loss aversion distorts Nash equilibrium predictions
- [[social-psychology-and-group-dynamics]] — Group framing effects and collective loss aversion in crowd behavior and financial panics
- [[attachment-theory-and-relationships]] — Loss aversion amplified in anxious-preoccupied attachment; relationship loss framed as catastrophic
- [[stoicism-and-stoic-philosophy]] — Stoic negative visualization as a systematic antidote to loss aversion and endowment effect
- [[dopamine-reward-systems-neuroscience]] — Amygdala and striatum dopamine circuits as the neural substrate of the asymmetric value function
- [[Finance/behavioral-finance-and-investor-psychology]] — Disposition effect, equity premium puzzle, and myopic loss aversion as market-level Prospect Theory applications
- [[Finance/value-investing-methodology]] — Margin of safety and contrarian investing as structured responses to the market's collective loss aversion
- [[Geopolitics/2026-05-27-us-china-great-power-competition]] — Loss aversion in geopolitical competition: status quo powers vs. revisionist challengers
