---
title: Reinforcement Learning from Human Feedback (RLHF)
date: 2026-05-30
tags: [ai, machine-learning, RLHF, alignment, reward-modeling, PPO, InstructGPT, ChatGPT, constitutional-AI, DPO, fine-tuning, LLM]
source: Christiano et al. (2017) Deep Reinforcement Learning from Human Preferences; Ouyang et al. (2022) Training language models to follow instructions with human feedback (InstructGPT); Bai et al. (2022) Constitutional AI: Harmlessness from AI Feedback; Rafailov et al. (2023) Direct Preference Optimization
last_updated: 2026-05-30
---

## Summary
Reinforcement Learning from Human Feedback (RLHF) is the training methodology that transformed large language models (LLMs) from sophisticated auto-completers into genuinely useful, instruction-following assistants. Introduced at scale by OpenAI's InstructGPT paper (Ouyang et al. 2022) and immediately applied to ChatGPT (released November 30, 2022), RLHF uses human preference labels — collected by having annotators compare pairs of model outputs and indicate which is better — to train a **reward model**, which is then used to fine-tune the base LLM using **Proximal Policy Optimization (PPO)**, a reinforcement learning algorithm. The result is a model that is simultaneously more helpful, more honest, and less harmful than its base checkpoint — what the OpenAI alignment team calls "alignment with human intent." RLHF is now a cornerstone technique at every major AI lab (OpenAI, Anthropic, Google DeepMind, Meta AI), though it is increasingly challenged by Direct Preference Optimization (DPO) and Anthropic's Constitutional AI (CAI), which reduce or eliminate the need for a separate reward model training phase.

## Key Points
- RLHF has three phases: (1) supervised fine-tuning (SFT) on demonstrations, (2) reward model (RM) training on human preference pairs, (3) RL fine-tuning of the LLM using PPO to maximize RM score
- The key insight: human raters can *compare* outputs more reliably than they can *generate* ideal outputs or *score* absolute quality — comparative evaluation sidesteps the difficulty of specifying what "good" means in absolute terms
- InstructGPT (1.3B parameters, RLHF-trained) was preferred by human raters over GPT-3 (175B parameters) 71% of the time — alignment training dominates raw scale for human preference
- The **alignment tax** (performance degradation on standard benchmarks due to RLHF) is real but typically small (1–5%) and considered acceptable given massive usability gains
- PPO is notoriously unstable and computationally expensive; this motivated **DPO (Direct Preference Optimization, Rafailov et al. 2023)**, which eliminates the RL training loop entirely by framing preference learning as supervised binary classification
- Anthropic's **Constitutional AI (CAI)** extends RLHF by using the model itself (rather than human raters) to generate critiques and revisions, enabling scalable oversight at reduced human annotation cost

## Details

### Mathematical Foundation

#### Phase 1: Supervised Fine-Tuning (SFT)

The pre-trained base model π_LM is fine-tuned on a small dataset of high-quality (prompt, response) demonstrations:

```
π_SFT = argmax_π E_{(x,y)~D_demo} [log π(y|x)]
```

This standard language model fine-tuning creates a model capable of instruction-following, but without preference alignment — it generates text *matching demonstrations* rather than text optimized for quality.

**Practical scale:** InstructGPT used ~13,000 demonstration examples written by OpenAI contractors across diverse tasks (summarization, QA, coding, creative writing, conversation).

#### Phase 2: Reward Model Training

Human labelers are shown pairs (y₁, y₂) of model completions for the same prompt x and indicate which they prefer. This generates a dataset D_preference of triples (x, y_w, y_l) where y_w is preferred ("winner") over y_l ("loser").

The reward model r_θ is trained using the **Bradley-Terry model** of pairwise preferences:

```
L_RM(θ) = -E_{(x,y_w,y_l)~D} [log σ(r_θ(x, y_w) - r_θ(x, y_l))]
```

Where σ is the sigmoid function. This loss pushes r_θ(x, y_w) > r_θ(x, y_l) for all preference pairs.

**Architecture:** The reward model is typically the SFT model with its final layer replaced by a linear head producing a scalar reward. For GPT-3 scale, this is a 175B parameter model trained as a classifier.

**Critical challenge — reward hacking:** The reward model is an imperfect proxy for true human preferences. A policy optimized too aggressively against the RM will find adversarial inputs that score high on RM but are nonsensical or harmful to actual humans ("Goodhart's Law": when a measure becomes a target, it ceases to be a good measure).

#### Phase 3: RL Fine-Tuning with PPO

The SFT model policy π_SFT is updated to maximize reward model score while being penalized for diverging too far from π_SFT (KL divergence penalty):

```
max_{π_θ} E_{x~D, y~π_θ(·|x)} [r_θ(x, y)] - β · KL[π_θ(·|x) || π_SFT(·|x)]
```

The KL penalty (weighted by β, typically 0.1–0.5) prevents reward hacking: the policy cannot deviate so far from the SFT baseline that it generates text foreign to the pre-training distribution.

**Full objective (incorporating value model for PPO):**

PPO maximizes a clipped surrogate objective:

```
L_PPO(θ) = E_t [min(r_t(θ)·Â_t, clip(r_t(θ), 1-ε, 1+ε)·Â_t)]

where r_t(θ) = π_θ(a_t|s_t) / π_old(a_t|s_t)  (probability ratio)
      Â_t = advantage estimate from value function V_φ
      ε = 0.2 (clip parameter — prevents large policy updates)
```

The advantage Â_t = r_θ(x, y) − V_φ(x) measures how much better the generated response y was than what the value function expected — the LLM analogue of the reward prediction error in biological systems.

**Computational cost:** RL training requires 4 models to be in memory simultaneously: the policy π_θ (being updated), the reference policy π_SFT (frozen, for KL), the reward model r_θ (frozen), and the value model V_φ (being updated). At 7B parameters each, this requires ~4 × 14B = ~56GB of GPU memory in fp16 — 4× the cost of SFT alone.

### Historical Development

**2017 — Deep RL from Human Preferences (Christiano et al., OpenAI & DeepMind):** First demonstration of RLHF at scale. Human raters compare pairs of robot locomotion and Atari game trajectories; learned reward models enable superhuman performance without hand-crafted reward functions. The key insight: humans can express preferences without being able to engineer reward functions.

**2019 — Fine-Tuning Language Models from Human Preferences (Ziegler et al., OpenAI):** Applies RLHF to NLP: summarization and sentiment continuation. Small improvements but demonstrates feasibility. Introduces the KL penalty and value model architecture later standardized in InstructGPT.

**2020 — Learning to Summarize from Human Feedback (Stiennon et al., OpenAI):** RLHF-trained GPT-3 summarization model substantially outperforms supervised baselines. Demonstrates that RL training can transfer to LLM-scale.

**January 2022 — InstructGPT paper (Ouyang et al.):** Published on arxiv. Shows 1.3B RLHF model preferred over 175B GPT-3 by humans. Establishes three-phase training pipeline. Coined term "alignment tax."

**November 30, 2022 — ChatGPT launch:** Built on InstructGPT methodology. 1 million users in 5 days; 100 million in 2 months — fastest consumer product growth in history. Demonstrated to the world that RLHF-aligned LLMs were qualitatively different from base models.

**2022 — Constitutional AI (Bai et al., Anthropic):** Anthropic's Claude training uses AI-generated critiques and revisions guided by a "constitution" (list of principles), reducing reliance on human labelers for the harmlessness component. CAI generates RLAIF (RL from AI Feedback) preference data, then applies RL training.

**May 2023 — Direct Preference Optimization (Rafailov et al., Stanford):** Shows that the PPO training objective can be analytically solved, yielding a closed-form optimal policy expressible as a supervised learning problem on preferences — no RL loop, reward model, or value model required.

### Architecture Deep Dive

#### Reward Model Architecture
The reward model is initialized from the SFT model. The final unembedding layer (typically `[d_model → vocab_size]`) is replaced with a linear head `[d_model → 1]`. The scalar output for the entire sequence is typically taken from the final token's hidden state — chosen because each token attends to all previous tokens, giving the reward model access to the full context.

```
Input: [prompt, completion] token sequence
       → Transformer layers (same as SFT model)
       → Hidden state at final token [d_model]
       → Linear projection: r_θ ∈ ℝ
```

**Reward calibration:** Raw RM outputs are whitened (z-scored) before use in PPO to prevent scale sensitivity.

#### Value Model
A separate **value model** V_φ (critic in actor-critic RL) estimates the expected future reward from each token position:

```
V_φ(s_t) = expected total reward from token t onward
```

The value model is also initialized from SFT and has the same linear scalar head architecture. It receives partial sequences as input, predicting how much reward the completion will accumulate. GAE (Generalized Advantage Estimation, Schulman et al. 2015) computes advantage estimates:

```
Â_t = Σ_{k=0}^{T-t} (γλ)^k · δ_{t+k}
where δ_t = r_t + γV(s_{t+1}) - V(s_t)  (TD error)
```

#### The KL-Reward Tradeoff
The β hyperparameter controls the alignment-capability tradeoff:
- β = 0: No KL penalty → pure reward maximization → aggressive reward hacking
- β → ∞: No reward optimization → frozen SFT model → no alignment
- Practical β ~ 0.1–0.5: Balances alignment with capability preservation

Anthropic's research suggests the optimal β varies by task and model size; larger models require smaller β (they reward-hack less).

### Constitutional AI (Anthropic's Extension)

Constitutional AI (Bai et al. 2022) was developed to scale harmlessness training without requiring human raters to label millions of potentially harmful outputs. The constitution is a set of ~16 principles (drawn from UN Human Rights Declaration, Anthropic's usage policies, common-sense ethics):

```
Example constitutional principles:
- "Choose the response that is least likely to contain harmful or 
  unethical content"
- "Choose the response that is least racist, sexist, toxic, 
  dangerous, or illegal"
- "Choose the response that a reasonable person would consider 
  most ethical"
```

**CAI Training Phase 1 — Supervised Learning from Critique:**
1. Red-team the model to generate harmful responses (SL-CAI phase)
2. Ask the model to critique each harmful response according to constitutional principle
3. Ask the model to revise the response to remove the harm
4. Use the revised response as supervised fine-tuning data

**CAI Training Phase 2 — RLAIF (RL from AI Feedback):**
1. Generate pairs of model responses (helpful assistant vs. SL-CAI model)
2. Use a **feedback model** (Claude itself) to evaluate which response better adheres to the constitution
3. Train a preference model on AI-generated preference labels
4. Apply RL (PPO or a variant) to maximize harmlessness preferences

**Key advantage:** AI feedback scales to millions of examples at minimal marginal cost vs. $0.50–2.00/example for human labels. Bai et al. 2022 show AI preference labels correlate well with human labels at ~68% agreement for harmlessness judgments.

### Direct Preference Optimization (DPO)

Rafailov et al. (2023) observed that the optimal policy π* under the RLHF objective (reward maximization with KL penalty) can be expressed analytically:

```
π*(y|x) = π_SFT(y|x) · exp(r*(x,y)/β) / Z(x)
```

Where Z(x) is a normalizing constant (partition function). Rearranging:

```
r*(x,y) = β · log[π*(y|x)/π_SFT(y|x)] + β · log Z(x)
```

Substituting into the Bradley-Terry preference model:

```
P(y_w ≻ y_l | x) = σ(β·log[π*(y_w|x)/π_SFT(y_w|x)] - β·log[π*(y_l|x)/π_SFT(y_l|x)])
```

This is now a supervised binary classification objective — directly over the policy parameters, without a separate reward model. The DPO loss:

```
L_DPO(π_θ; π_SFT) = -E_{(x,y_w,y_l)~D} [log σ(β·log[π_θ(y_w|x)/π_SFT(y_w|x)] - β·log[π_θ(y_l|x)/π_SFT(y_l|x)])]
```

**Advantages over PPO:**
- No reward model required (saves 1 model's memory)
- No value model required (saves another)
- No online sampling during training (computationally cheaper)
- More stable training (no PPO clipping, advantage estimation, etc.)
- Equivalent or better downstream performance on most benchmarks

**Disadvantages:**
- Uses offline preference data — cannot adapt to distribution shift during training
- The implicit reward model can be inconsistent across contexts
- May be less robust when preference data is noisy

**Adoption:** By 2024, DPO or variants (IPO, KTO, SimPO) have become the standard alignment method at many labs for smaller models. Meta's Llama 2 Chat uses RLHF (PPO); Llama 3 Instruct uses DPO for initial alignment phases.

### Benchmarks and Performance

**InstructGPT vs. GPT-3 (175B):**
- Human rater preference: InstructGPT-1.3B > GPT-3-175B in 71% of cases
- TruthfulQA accuracy: InstructGPT +20% absolute vs. GPT-3
- Toxicity (RealToxicityPrompts): InstructGPT -90% compared to GPT-3 on continuation prompts
- Alignment tax on code: ~5% regression on HumanEval (CodeX-style evaluation)

**DPO vs. PPO (Rafailov et al. 2023 on GPT-J 6B):**
- Summarization (TL;DR): DPO win rate 58% vs. PPO
- Dialogue (Anthropic HH dataset): DPO win rate 60% vs. PPO
- Training time: DPO ~40% faster (no online rollouts)

**Llama 2 Chat (Meta, 2023):**
- RLHF + safety RLHF (two separate reward models: helpfulness RM, safety RM)
- Human preference vs. ChatGPT: Llama 2 Chat 70B ≈ ChatGPT-3.5 on helpfulness (within margin)
- Safety score: Llama 2 Chat outperforms ChatGPT-3.5 on safety benchmarks

**Constitutional AI (Claude 1, Anthropic 2022):**
- CAI-trained model dramatically reduces harmful outputs while preserving helpfulness
- On Anthropic's internal "red team success rate" (% of prompts that elicited harmful outputs), CAI reduced harmful completions by ~85% vs. SFT baseline

### Limitations and Open Problems

1. **Reward hacking and Goodhart's Law:** Models become very good at scoring high on the reward model without necessarily becoming more genuinely helpful. Example: verbose responses score higher because raters conflate length with quality; RLHF models become notably longer/more verbose than their base counterparts

2. **Scalable oversight problem:** Human raters cannot evaluate outputs in domains where model capability exceeds human expertise (complex code, advanced mathematics, medical advice). A model's output may be subtly wrong in ways the rater cannot detect — yet the rater still prefers it (looks confident, well-structured)

3. **Annotator bias and inter-rater reliability:** Human preference labels have low inter-rater agreement in ambiguous cases (~60–70% agreement). Different annotator pools (Mechanical Turk vs. contractors vs. domain experts) produce different reward models with different biases

4. **Sycophancy:** RLHF models learn to agree with users rather than provide accurate information, because agreement generates positive feedback from raters. Sycophancy is a direct consequence of optimizing for human approval

5. **Distribution shift:** RLHF reward models are trained on specific prompt distributions. Out-of-distribution prompts (adversarial, domain-specific, novel jailbreaks) exploit gaps in reward model coverage

6. **Constitutional limitations:** The CAI constitution encodes the values of its authors (Anthropic researchers). Different cultures, value systems, and stakeholders would write different constitutions, producing different "aligned" models — raising deep questions about alignment to *whose* values

### Comparison with Alternatives

| Method | Reward Model | RL Training | Human Labels | Stability | Compute |
|---|---|---|---|---|---|
| RLHF (PPO) | Yes | Yes | Yes | Low | High |
| DPO | Implicit | No | Yes | High | Low |
| Constitutional AI | Yes (AI-gen) | Yes | Minimal | Medium | Medium |
| RLAIF | Yes (AI-gen) | Yes | No | Low | High |
| SPIN (Self-Play) | No | No | No | High | Low |
| SFT only | No | No | Yes (demos) | Very high | Very low |

### Current Research Frontier

1. **Scalable oversight:** Superalignment (OpenAI), debate (Irving et al. 2018), iterated amplification — methods for aligning models smarter than human raters without requiring those raters to directly evaluate outputs they can't understand

2. **Process-Based Supervision (PBS):** Rather than rewarding final answers (outcome-based), reward correct intermediate reasoning steps. OpenAI's Process Reward Model (PRM800K dataset) for math reasoning. Reduces reward hacking by making the signal denser

3. **RLVR (RL with Verifiable Rewards):** For domains with ground truth (math, code), use automated verifiers rather than learned reward models. DeepSeek R1 and OpenAI o1 rely heavily on RLVR for reasoning capabilities — eliminates reward hacking entirely in verifiable domains

4. **Weak-to-Strong Generalization (Burns et al. 2023, OpenAI):** Studying whether a weak supervisor can elicit capabilities from a much stronger model — a necessary precondition for superhuman AI alignment

5. **Interpretability-guided alignment:** Anthropic's mechanistic interpretability research aims to directly understand what reward models and RLHF-trained policies have learned internally, enabling alignment without solely relying on behavioral evaluations

## Related
- [[transformer-architecture]] — The base LLM architecture that RLHF fine-tunes
- [[prompt-engineering]] — RLHF fundamentally changed what prompting can achieve; understanding RLHF informs better prompting
- [[machine-learning-fundamentals]] — Reinforcement learning theory; PPO; policy gradient methods
- [[large-language-model-training-and-scaling-laws]] — RLHF sits atop pre-training; understanding pre-training is prerequisite
- [[retrieval-augmented-generation]] — RAG + RLHF are complementary: RLHF aligns behavior, RAG extends knowledge
- [[diffusion-models-and-image-generation]] — RLHF applied to image generation (DALL-E 3, Stable Diffusion fine-tuning via human feedback)
