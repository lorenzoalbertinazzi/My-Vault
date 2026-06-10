---
title: Reinforcement Learning from Human Feedback (RLHF)
date: 2026-05-30
tags: [ai, machine-learning, RLHF, alignment, reward-modeling, PPO, InstructGPT, ChatGPT, constitutional-AI, DPO, fine-tuning, LLM, RLAIF, GRPO, preference-optimization, online-RL, reward-hacking, KL-divergence, SFT]
source: "Christiano et al. (2017) Deep RL from Human Preferences (arXiv:1706.03741); Ouyang et al. (2022) InstructGPT (arXiv:2203.02155); Bai et al. (2022) Constitutional AI (arXiv:2212.08073); Rafailov et al. (2023) Direct Preference Optimization (arXiv:2305.18290); Schulman et al. (2017) PPO (arXiv:1707.06347); Stiennon et al. (2020) Learning to Summarize from Human Feedback (arXiv:2009.01325)"
last_updated: 2026-06-10
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

### GRPO: Group Relative Policy Optimization (DeepSeek, 2024)

DeepSeek's contribution to post-training methodology, introduced in DeepSeek-Math (2024) and used at scale in DeepSeek-R1 (January 2025), Group Relative Policy Optimization (GRPO) eliminates the value/critic model from PPO — halving memory requirements and simplifying training dramatically.

**Core Innovation — Baseline from Group Sampling:**
```
PPO requires a value model V_φ(s_t) to estimate baseline expected reward.
GRPO replaces this with: sample G responses for each prompt, 
use their mean reward as the baseline.

For prompt x, sample G completions {y₁, y₂, ..., y_G} ~ π_old(·|x):
    Advantage for response y_i:
    
    Â_i = (r_i - mean({r₁,...,r_G})) / std({r₁,...,r_G})
    
    where r_i = reward for response y_i
```

**GRPO Objective:**
```
L_GRPO(θ) = E_{x~D, {y_i}~π_old} [
    (1/G) Σ_{i=1}^{G} min(
        π_θ(y_i|x)/π_old(y_i|x) · Â_i,
        clip(π_θ(y_i|x)/π_old(y_i|x), 1-ε, 1+ε) · Â_i
    )
] - β · KL[π_θ || π_ref]

Where:
  G = group size (typically 8–32)
  ε = clip ratio (0.2, same as PPO)
  β = KL penalty coefficient
  π_ref = reference policy (SFT model, frozen)
```

**Memory Comparison (70B model):**
| Method | Policy | Value Model | Reward Model | Reference | Total |
|---|---|---|---|---|---|
| PPO (full) | 70B (fp16) | 70B (fp16) | 7–70B | 70B | ~4–6× model size |
| GRPO | 70B (fp16) | None | 7–70B | 70B | ~3× model size |
| DPO | 70B (fp16) | None | None (implicit) | 70B | ~2× model size |

**Why GRPO Works — The Intuition:**
Instead of a learned value function (which is an approximation), GRPO uses empirical Monte Carlo estimation of the baseline. For mathematical reasoning tasks, this is actually better because:
- The value function for reasoning chains is notoriously hard to learn (sparse rewards at final answer)
- Group sampling directly observes which completions score higher — no approximation
- The "group" naturally captures prompt-conditional variance, not just global

**GRPO Implementation — Key Details:**
```python
def grpo_update(model, ref_model, reward_model, batch, G=8, epsilon=0.2, beta=0.001):
    """
    batch: list of prompts
    G: group size (responses sampled per prompt)
    """
    all_advantages = []
    all_log_probs = []
    all_ref_log_probs = []
    
    for prompt in batch:
        # Sample G responses from current policy
        responses = model.sample(prompt, n=G, temperature=0.8, max_tokens=2048)
        
        # Compute rewards (verifier, reward model, or composite)
        rewards = [reward_model.score(prompt, r) for r in responses]
        
        # Group normalization (the key GRPO step)
        mean_r = np.mean(rewards)
        std_r = np.std(rewards) + 1e-8  # Avoid division by zero
        advantages = [(r - mean_r) / std_r for r in rewards]
        
        # Log probabilities under current and reference policy
        for resp, adv in zip(responses, advantages):
            log_prob = model.log_prob(prompt, resp)
            ref_log_prob = ref_model.log_prob(prompt, resp)  # Frozen
            
            all_advantages.append(adv)
            all_log_probs.append(log_prob)
            all_ref_log_probs.append(ref_log_prob)
    
    # Clipped policy gradient (same as PPO)
    ratios = torch.exp(torch.stack(all_log_probs) - 
                       torch.stack(all_log_probs).detach())
    
    policy_loss = -torch.mean(
        torch.min(
            ratios * torch.tensor(all_advantages),
            torch.clamp(ratios, 1-epsilon, 1+epsilon) * torch.tensor(all_advantages)
        )
    )
    
    # KL penalty against reference policy
    kl_loss = torch.mean(
        torch.stack(all_log_probs) - torch.stack(all_ref_log_probs)
    )
    
    total_loss = policy_loss + beta * kl_loss
    total_loss.backward()
    return total_loss
```

**DeepSeek-R1 Results with GRPO:**
```
DeepSeek-R1 (January 2025) — Key Results:
  - Training compute: ~2,000 H800 GPU-hours for RL phase (vs. OpenAI o1: unknown but estimated 10×+)
  - AIME 2024: 79.8% (vs. OpenAI o1: 79.2%) — matching at dramatically lower cost
  - MATH-500: 97.3% (vs. o1: 96.4%) — slight edge
  - LiveCodeBench (code): 65.9% (vs. o1: 63.4%)
  - Cost per output token: ~$0.55/million (vs. o1: $15/million at launch) — 27× cheaper
  
  GRPO enabled this by:
  1. Removing value model (saves 50% RL training memory → enables larger batch sizes)
  2. Group sampling surfaces reasoning diversity within a prompt (better signal than single-sample PPO)
  3. Stable training — fewer hyperparameters to tune than PPO
```

### Process Reward Models (PRMs) — Supervising Reasoning Steps

Outcome-based reward models score only the final answer: correct or incorrect. This creates sparse reward problems for long reasoning chains and enables "reward hacking" through lucky guesses.

**Process Reward Models** (Lightman et al., OpenAI, 2023) supervise every intermediate step:

**Motivation:**
```
Math problem with 10 reasoning steps:
  Outcome-Based RM (ORM): 
    Score = 1 if final answer correct, 0 otherwise
    Problem: Model gets reward for wrong reasoning that reaches right answer
             OR gets no reward for correct reasoning that makes an arithmetic error at step 9
  
  Process-Based RM (PRM):
    Score = Σ_t s_t where s_t ∈ {correct, incorrect, neutral} for each step
    Problem: Requires step-level human annotations (expensive)
```

**OpenAI PRM800K Dataset:**
```
Scale: 800,000 step-level human labels on math reasoning chains
Collection: Human labelers evaluated each step of GPT-4 solutions to MATH dataset
Label types:
  + (positive): Step is correct and useful
  - (negative): Step contains an error
  ~ (neutral): Step is redundant but not wrong

Cost: Estimated $2–5M in labeler time
Result: PRM trained on PRM800K dramatically outperforms ORM on math competition problems
```

**PRM Architecture:**
```python
class ProcessRewardModel(nn.Module):
    """
    Takes (prompt, partial_solution) at each step
    and outputs probability that step is correct.
    """
    def __init__(self, base_model, d_model=4096):
        super().__init__()
        self.transformer = base_model  # Same architecture as policy
        self.step_head = nn.Linear(d_model, 1)  # Scalar per step
        
    def forward(self, prompt, solution_so_far):
        # Append special <step_end> token after each reasoning step
        # Hidden state at <step_end> positions → step scores
        tokens = tokenize(prompt + solution_so_far)
        hidden_states = self.transformer(tokens)  # [seq_len, d_model]
        
        # Extract hidden states at step-end token positions
        step_positions = find_step_end_positions(tokens)
        step_hidden = hidden_states[step_positions]  # [n_steps, d_model]
        
        step_scores = self.step_head(step_hidden).squeeze(-1)  # [n_steps]
        step_probs = torch.sigmoid(step_scores)  # P(step correct)
        
        # Process reward: geometric mean of step probabilities
        # (any bad step → low overall score)
        process_reward = torch.prod(step_probs)
        return process_reward, step_probs

# Training loss: binary cross-entropy at each step position
def prm_loss(predicted_step_probs, human_labels):
    # labels: 1 for positive, 0 for negative, ignored for neutral
    valid_mask = (human_labels != NEUTRAL)
    loss = F.binary_cross_entropy(
        predicted_step_probs[valid_mask],
        human_labels[valid_mask].float()
    )
    return loss
```

**PRM vs. ORM — Performance (OpenAI, 2023):**
```
MATH dataset competition problems, Best-of-N sampling:
  ORM Best-of-8:    78.2% accuracy
  PRM Best-of-8:    84.3% accuracy  (+6.1 points)
  PRM Best-of-1860: ~96% accuracy   (near-ceiling with sufficient samples)
  
  This demonstrates: PRMs are better at selecting correct solutions
  from a set of candidates than outcome-based models.

PRM enables "process-graded" RL training:
  - Denser reward signal → faster convergence
  - Better credit assignment for long reasoning chains
  - Reduced reward hacking (can't fake correct intermediate steps)
```

**Practical PRM Deployment — Monte Carlo Step Estimation:**
```
Full human labeling ($2-5M per dataset) is not scalable.
Alternative: Math-Shepherd (Wang et al. 2024) uses Monte Carlo estimation:

For each partial solution prefix p_t:
  1. Sample K completions from this prefix: {c_{t,1}, ..., c_{t,K}}
  2. Check whether each completion reaches a correct final answer
  3. Estimate p(step_t is correct) ≈ fraction of completions reaching correct answer
  
  mc_prm_score(p_t) = K_correct / K_total

Advantage: No human labelers needed
Disadvantage: Computationally expensive (K×T model calls per example)
Cost: For K=8, T=10 steps, 100K training examples:
     8 × 10 × 100,000 = 8 million additional inference calls
```

### RLVR: RL with Verifiable Rewards

RLVR (Reinforcement Learning with Verifiable Rewards) is the training methodology behind the 2025 generation of "reasoning models": OpenAI o1/o3, DeepSeek-R1, Gemini Thinking, Claude Extended Thinking (3.7/3.5). It sidesteps both the reward hacking problem and the scalable oversight problem by replacing learned reward models with deterministic verifiers.

**Core Insight:**
```
For verifiable domains (math, code, formal logic, factual QA with clear answers):
  A learned reward model r_θ(x, y) can be imperfect and hackable.
  A deterministic verifier V(x, y) → {0, 1} is perfect and unhackable.
  
  RL objective becomes:
    max_π E_{x~D} [V(x, π(x))]
    
  Where V checks:
    Math: Does the final answer equal the ground truth? (symbolic comparison)
    Code: Do all test cases pass? (code execution)
    Logic: Is the proof formally valid? (automated theorem prover)
    Trivia: Does the answer match the reference? (string/entity matching)
```

**The RLVR Training Loop:**
```python
def rlvr_training_step(policy, ref_policy, verifier, prompts, 
                        G=8, epsilon=0.2, beta=0.001):
    """
    RLVR with GRPO-style group advantage estimation.
    Reward is verifier binary {0, 1} rather than learned reward model.
    """
    policy_losses = []
    
    for prompt, ground_truth in prompts:
        # Sample G thinking + answer completions
        completions = policy.sample(
            prompt, 
            n=G, 
            max_tokens=4096,  # Allow long chain-of-thought
            format="<think>...</think>\n<answer>...</answer>"
        )
        
        # Verifier rewards: binary, unambiguous
        rewards = []
        for completion in completions:
            answer = extract_answer(completion)
            reward = verifier(prompt, answer, ground_truth)
            # Optional: bonus for concise thinking (length penalty)
            reward -= 0.001 * len(completion)  # Length regularization
            rewards.append(reward)
        
        # GRPO advantage normalization
        mean_r = np.mean(rewards)
        std_r = np.std(rewards) + 1e-8
        advantages = [(r - mean_r) / std_r for r in rewards]
        
        # Policy gradient update (PPO-clip)
        for completion, adv in zip(completions, advantages):
            log_prob = policy.log_prob(prompt, completion)
            ref_log_prob = ref_policy.log_prob(prompt, completion)
            ratio = torch.exp(log_prob - log_prob.detach())
            
            policy_loss = -torch.min(
                ratio * adv,
                torch.clamp(ratio, 1-epsilon, 1+epsilon) * adv
            )
            kl = log_prob - ref_log_prob  # KL penalty
            policy_losses.append(policy_loss + beta * kl)
    
    total_loss = torch.mean(torch.stack(policy_losses))
    total_loss.backward()
    return total_loss

class MathVerifier:
    """Verifies math answers using symbolic comparison"""
    def __call__(self, prompt, answer, ground_truth):
        try:
            # Normalize: remove $, spaces, convert LaTeX fractions
            a = sympy.sympify(normalize_math(answer))
            gt = sympy.sympify(normalize_math(ground_truth))
            return 1.0 if sympy.simplify(a - gt) == 0 else 0.0
        except:
            return 0.0  # Parse error = wrong answer

class CodeVerifier:
    """Runs test cases against generated code"""
    def __call__(self, prompt, code, test_cases):
        try:
            passed = 0
            for test_input, expected_output in test_cases:
                actual = sandbox_execute(code, test_input, timeout=5)
                if actual == expected_output:
                    passed += 1
            return passed / len(test_cases)  # Partial credit for partial pass
        except TimeoutError:
            return 0.0
        except Exception:
            return 0.0
```

**The "Aha Moment" — Emergent Thinking Behavior:**
```
DeepSeek-R1 training observations (DeepSeek-AI, 2025):
  
  During RLVR training, the model spontaneously developed:
  1. "Wait, let me reconsider..." patterns — backtracking when stuck
  2. Multi-strategy exploration within a single response
  3. Verification steps: "Let me check: 3 × 7 = 21 ✓"
  4. Self-correction: "Actually, I made an error above..."
  
  None of these behaviors were explicitly trained via demonstrations.
  They emerged purely from RL signal (get the right answer).
  
  DeepSeek team called this the "Aha moment" — a phase transition
  in training where the model discovered that longer, more careful
  reasoning chains achieve higher reward.

The model learned to think not because it was told to,
but because thinking more carefully led to correct answers.
```

**RLVR vs. RLHF: Fundamental Distinction:**
| Dimension | RLHF | RLVR |
|---|---|---|
| Reward source | Human preferences (subjective) | Deterministic verifier (objective) |
| Domains | Open-ended: helpfulness, safety, style | Verifiable: math, code, logic |
| Reward hacking | Yes — optimizes for appearance | No — can't fake correct answer |
| Scalable oversight | Required (human in the loop) | Not needed (verifier is superhuman rater) |
| Label cost | $0.50–$2.00 per preference pair | ~$0 (automated verification) |
| Applicable to | Any LLM task | Only tasks with ground truth |
| Key limitation | Goodhart's Law applies | Distributional shift in problem types |

**RLVR Compute Costs (Approximate, 2025):**
```
DeepSeek-R1-Zero (RL from scratch on DeepSeek-V3 base):
  - Model size: 671B MoE parameters (37B active)
  - RL training: ~10,000 H800 GPU-hours (~$300,000 compute cost)
  - Prompts: ~100K MATH/AIME/competition math problems
  - Samples per prompt: G = 8–16
  - Training duration: ~3 weeks
  
OpenAI o1 (estimated):
  - Model size: Unknown (estimated 200B+ parameters)
  - RL training: Unknown (estimated 5–20× DeepSeek cost)
  - Competitive AIME score: 83.3% (vs. DeepSeek-R1: 79.8%)
  
DeepSeek-R1-Distill (knowledge distillation from R1 to smaller models):
  - 7B/14B/32B models trained on R1's reasoning traces (SFT, no RL)
  - DeepSeek-R1-Distill-7B: 55.5% on AIME 2024 
    (surpasses o1-mini: 63.6% — within striking range)
  - Training cost: ~$50,000 (pure SFT on distilled data)
```

**Limitations of RLVR:**
```
1. Coverage problem: Only ~30% of LLM applications have verifiable answers
   (math, code, formal logic, trivia) vs. 70% that are subjective
   (writing, summarization, advice, explanation quality)

2. Distribution shift: RLVR training on MATH/AMC improves competition math
   but may degrade on informal/applied math (optimization on training distribution)

3. Overthinking: Models trained purely on RL develop verbose reasoning chains
   that are longer than necessary — increases inference cost 10–30×
   DeepSeek-R1 fix: length penalty in reward and rejection sampling with
   response length constraint

4. Reasoning verifiers are hard for some domains:
   - Multi-step code with I/O ambiguity
   - Proofs requiring semantic understanding (not just formal verification)
   - Scientific questions where "correct" depends on context

5. Cold start problem: Base models must already be somewhat capable at the task
   for RLVR to work — models that can't generate any correct answers in group
   sampling get no gradient signal (all rewards = 0, all advantages = 0)
   Fix: Start with SFT on demonstrations, then apply RLVR
```

### Post-Training Pipeline Architecture (2025)

Modern LLM post-training uses a multi-stage pipeline combining all methods:

```
Stage 0: Pre-training
  - Next-token prediction on 10T–100T tokens
  - Result: Capable but unhelpful base model
  
Stage 1: Supervised Fine-Tuning (SFT)
  - 10K–100K high-quality (prompt, response) pairs
  - Teaches instruction following format
  - Result: Helpful but not optimally aligned
  
Stage 2: Preference Learning (DPO or RLHF)
  - 100K–1M preference pairs
  - Trains helpfulness, safety, style
  - Methods: DPO (fast, cheap), PPO (expensive, powerful), GRPO (middle ground)
  - Result: Aligned assistant model
  
Stage 3: Domain-Specific RL (RLVR, for reasoning models)
  - Verifiable problem datasets (MATH, code challenges, etc.)
  - Uses GRPO or PPO with verifier rewards
  - Teaches extended chain-of-thought reasoning
  - Result: Reasoning model (o1, R1, Extended Thinking variants)
  
Stage 4: Distillation (optional, for efficiency)
  - Reasoning model generates traces on large dataset
  - Smaller model trained on these traces (SFT)
  - Result: Efficient small model with reasoning capabilities
```

**Approximate per-stage costs (70B model, 2025):**
| Stage | Data | Compute | Cost | Duration |
|---|---|---|---|---|
| SFT | 50K examples | 100 H100 GPU-hours | $300 | 4 hours |
| DPO | 200K pairs | 500 GPU-hours | $1,500 | 20 hours |
| PPO/GRPO | 100K prompts | 5,000 GPU-hours | $15,000 | 5 days |
| RLVR (math) | 100K problems × G=8 | 10,000 GPU-hours | $30,000 | 10 days |
| Distillation | 1M R1 traces | 2,000 GPU-hours | $6,000 | 3 days |

### Current Research Frontier

1. **Scalable oversight:** Superalignment (OpenAI), debate (Irving et al. 2018), iterated amplification — methods for aligning models smarter than human raters without requiring those raters to directly evaluate outputs they can't understand

2. **Process-Based Supervision (PBS):** Rather than rewarding final answers (outcome-based), reward correct intermediate reasoning steps. OpenAI's Process Reward Model (PRM800K dataset) for math reasoning. Reduces reward hacking by making the signal denser

3. **RLVR (RL with Verifiable Rewards):** For domains with ground truth (math, code), use automated verifiers rather than learned reward models. DeepSeek R1 and OpenAI o1 rely heavily on RLVR for reasoning capabilities — eliminates reward hacking entirely in verifiable domains

4. **Weak-to-Strong Generalization (Burns et al. 2023, OpenAI):** Studying whether a weak supervisor can elicit capabilities from a much stronger model — a necessary precondition for superhuman AI alignment

5. **Interpretability-guided alignment:** Anthropic's mechanistic interpretability research aims to directly understand what reward models and RLHF-trained policies have learned internally, enabling alignment without solely relying on behavioral evaluations

## Cross-Disciplinary Connections

### Behavioral Economics: Preference Elicitation and Revealed Preference
RLHF's pairwise comparison framework is a form of preference elicitation — a core methodology in behavioral economics. The assumption that pairwise comparisons reveal stable underlying preferences is challenged by decades of research: preferences are context-dependent, reference-point biased (Kahneman & Tversky prospect theory), constructed in-the-moment rather than retrieved from stable values (Lichtenstein & Slovic, 2006). These biases propagate directly into reward model training: the reward model learns human *judgment under cognitive bias* rather than human *true values*. Anchoring effects in rater evaluations mean that response order (A vs. B vs. B vs. A) systematically affects comparison outcomes.

### Psychology: Operant Conditioning and Reinforcement Schedules
RLHF is a computational implementation of Skinnerian operant conditioning: the policy (organism) produces outputs (behaviors) and receives scalar reward signals (reinforcement) that shape future behavior. The reward hacking problem — where the policy finds unexpected ways to maximize reward while violating intent — mirrors "unintended reinforcement" studied in animal learning (Breland & Breland's 1961 "misbehavior of organisms"). Variable ratio reinforcement schedules (unpredictable rewards) produce the most persistent behaviors in animal studies; the stochastic reward signals in RLHF training may similarly create harder-to-extinguish behavioral patterns, relevant for understanding why misaligned behaviors can be difficult to train away.

### Social Choice Theory: Aggregating Diverse Preferences
The reward model aggregates preferences from multiple human raters with genuinely different values into a single scalar signal — a social choice aggregation problem. Arrow's Impossibility Theorem (1951) proves that no consistent aggregation of individual ordinal preferences can simultaneously satisfy unanimity, independence of irrelevant alternatives, and non-dictatorship. RLHF implicitly performs this aggregation by majority vote or averaging, violating minority preferences. Constitutional AI replaces human raters with AI feedback, but raises the same question at a meta-level: the Constitution itself encodes value choices that are not democratically derived.

### Cognitive Science: Learning Theory, Interference, and Transfer
The SFT → RM → RL pipeline mirrors cognitive models of skill acquisition (Anderson's ACT-R theory): declarative knowledge (SFT demonstrations), development of an internal performance evaluator (RM), then proceduralization via reinforcement (RL fine-tuning). The *alignment tax* — RLHF-trained models sometimes lose pre-training capabilities — mirrors *catastrophic interference* (McCloskey & Cohen, 1989) in neural systems: new learning disrupts old memory traces. Elastic Weight Consolidation (EWC) and related continual learning methods were designed to address biological catastrophic forgetting and are now applied to mitigate the alignment tax.

### Political Philosophy: Deliberative Democracy and Value Legitimacy
The question "whose values should AI be aligned to?" is a foundational problem in political philosophy. Deliberative democracy theories (Habermas, Rawls) suggest legitimate norms emerge from inclusive, reasoned public discourse. Current RLHF data collection is far from this ideal: labeler pools are geographically concentrated (predominantly English-speaking, WEIRD demographics), not selected through any democratic process, and paid at piece-rate wages that incentivize speed over reflection. The emerging field of *participatory AI alignment* (Solaiman et al., 2023) attempts to apply deliberative democracy principles — stakeholder representation, structured deliberation, explicit value articulation — to preference data collection.

### Game Theory: Mechanism Design for Incentive Compatibility
The reward labeling setup is a mechanism design problem: designing the comparison task, rating guidelines, and payment structure so that raters' incentives are aligned with honest preference expression. Strategic labeling — raters expressing preferences they don't hold to influence model behavior — is a real risk in deployed labeling pipelines. Vickrey-Clarke-Groves (VCG) mechanisms and other incentive-compatible designs from auction theory could theoretically be applied to RLHF labeling to make honest reporting a dominant strategy. The multi-principal problem — the model simultaneously serves many users with different preferences — is a fundamental tension not addressed by current RLHF formulations. See [[game-theory-and-strategic-thinking]] for a deeper treatment of mechanism design and how Nash equilibria shape the incentive landscape of multi-agent labeling pipelines.

### Neuroscience: RLHF and the Dopamine Reward System

The architecture of RLHF is strikingly isomorphic to the mammalian dopamine reward system described in [[dopamine-reward-systems-neuroscience]]. The biological system works as follows: the ventral tegmental area (VTA) contains dopaminergic neurons that fire not when a reward is received, but when a reward is *better than expected* — the **reward prediction error (RPE)** signal. This signal (δ = r + γV(s') - V(s) in temporal-difference notation) is then broadcast throughout the prefrontal cortex and striatum, strengthening the synaptic pathways that led to the under-predicted reward. This is, mechanically, the same operation as the advantage function A(s,a) = Q(s,a) - V(s) in PPO: positive advantage signals increase action probability (reward prediction error > 0, "better than expected"), negative advantage signals decrease it (reward prediction error < 0, "worse than expected").

The parallel runs deeper than mere analogy. The **credit assignment problem** — determining which action in a long behavioral sequence caused a reward received much later — is the central challenge for both biological RL and artificial RL. The hippocampus is theorized to solve this via **replay**: during sleep, the brain replays sequences of recent experiences, propagating TD error signals backwards through the sequence to assign credit to causal actions (Dayan & Niv, 2008). The PPO training loop performs the identical function: storing rollouts in a replay buffer and then performing multiple gradient updates over those stored transitions, propagating the advantage signal backwards through the sequence. Evolution solved credit assignment in the brain; Sutton and Barto's temporal difference learning formalised the same solution in mathematics (1988).

The **reward hacking** problem in RLHF — policies finding unexpected ways to maximize the measured reward while violating intent — has a precise biological analog in **addiction**. Addictive substances hijack the dopamine system by triggering reward prediction errors that do not correspond to actual survival-relevant outcomes. The brain's "reward model" was trained by evolution on ancestral environments; novel stimuli (sugar, cocaine, social media dopamine loops) exploit the reward model's out-of-distribution behavior just as RLHF-trained models exploit reward models by producing long, confident-sounding responses that trigger high ratings regardless of accuracy. The solution being explored — process reward models (PRMs) that reward correct reasoning steps, not just final answers — mirrors the neuroscience insight that intrinsic motivation and learned predictive representations are more robust to reward hacking than pure external reward signals. See [[prospect-theory-and-decision-making]] for how humans systematically deviate from expected-value maximization in ways that propagate directly into preference labels collected for RLHF training.

### 2026 RLHF Evolution: RLAIF, DPO at Scale, and Constitutional AI v2

**RLAIF (Reinforcement Learning from AI Feedback) — Scaling Alignment Beyond Human Annotators:**
The bottleneck for RLHF at frontier scale is not compute or model capability — it is human annotation. Rating millions of response pairs requires thousands of annotators with domain expertise, and for complex technical domains (advanced mathematics, specialized coding, medical reasoning), finding annotators who can reliably evaluate response quality is extremely difficult and expensive. **RLAIF** (Reinforcement Learning from AI Feedback) addresses this by using a capable AI model as the preference rater rather than human annotators:

**The RLAIF protocol (Bai et al. 2022, Anthropic; Lee et al. 2023, Google):**
1. Generate candidate responses A and B from the policy model
2. Present both to a "judge" LLM (typically the current policy model or a larger model) with instructions to evaluate which is better according to specified criteria
3. Use the judge's preference as the training signal for the reward model
4. Fine-tune the policy via PPO or DPO using this AI-generated preference signal

**Quality comparison (Lee et al., 2023 "RLAIF vs. RLHF"):** RLAIF-trained models achieve comparable human preference ratings to RLHF-trained models on summarization and helpful assistant tasks, while requiring no human annotation (100× cost reduction per preference pair).

**Constitutional AI v2 (Anthropic, 2025):** Anthropic's evolved Constitutional AI framework combines RLAIF with an explicit multi-level constitution:
- **Level 1 (Core values):** Harm avoidance, honesty, and helpfulness principles — evaluated by a specialized "harmlessness" judge LLM trained to detect subtle harms
- **Level 2 (Behavioral guidelines):** Domain-specific rules (medical advice caution, legal disclaimer requirements, content policy specifics) evaluated by domain-specialized judges
- **Level 3 (User preferences):** Style, tone, and format preferences evaluated using the general-purpose judge model
The multi-level evaluation allows the training signal to precisely target different aspects of model behavior independently, rather than conflating harm avoidance and helpfulness in a single preference signal.

**Direct Preference Optimization (DPO) — Replacing PPO at Scale:**
DPO (Rafailov et al., 2023) offered a simpler alternative to PPO-based RLHF: rather than training an explicit reward model and using RL to optimize against it, DPO directly optimizes the policy using preference pairs through a closed-form solution:

```
L_DPO(π_θ) = -𝔼_{(x,y_w,y_l)} [log σ(β log(π_θ(y_w|x)/π_ref(y_w|x)) - β log(π_θ(y_l|x)/π_ref(y_l|x)))]
```

Where y_w is the preferred response, y_l is the dispreferred response, π_ref is the reference (pre-RLHF) model, and β controls the KL divergence penalty. DPO advantages: no separate reward model (simplifies pipeline), no RL instability (directly maximizes likelihood of preferred vs. dispreferred), 3–5× less compute than PPO.

By 2026, DPO has become the standard alignment fine-tuning method for most open-source models (Llama 3 family, Mistral, Qwen), while PPO remains preferred for frontier labs where the reward model's ability to provide nuanced scalar feedback justifies the complexity. **SimPO** (Meng et al., 2024) and **IPO** (Azar et al., 2024) are improved DPO variants addressing instability in out-of-distribution cases.

**Process Reward Models (PRMs): Rewarding the Journey, Not the Destination:**
A fundamental critique of outcome-based RLHF (rewarding correct final answers) is that it doesn't penalize correct-answer-wrong-reasoning — a model that gets the right answer for the wrong reason will be reinforced for that reasoning. **Process Reward Models** provide step-level feedback:

**PRM construction:** Human annotators (or AI judges) evaluate each reasoning step: "Is this step logically valid and relevant given the prior steps?" The PRM is trained to predict step-level correctness from the partial reasoning trace.

**Training with PRMs:** During RL training, rewards are accumulated at each reasoning step rather than only at the final token. This forces the policy to learn genuinely valid reasoning chains rather than reverse-engineering correct answers.

**Empirical results:** OpenAI's Math-Shepherd (2024) trained PRMs on mathematics reasoning and demonstrated that PRM-guided beam search finds correct solutions 5–7% more often than outcome-supervised models on MATH benchmark at the same test-time compute budget. The PRM approach is central to o1/o3's test-time compute scaling — PRMs guide Monte Carlo Tree Search over reasoning chains to find the most valid path to a solution.

**RLHF for Agentic Systems:**
Training AI agents (multi-step, tool-using) requires extending RLHF beyond single-turn preference pairs to **multi-step feedback**:

**WebArena RLHF (2025):** Training browser agents using task completion success as the reward signal — whether the agent successfully books a flight, finds the requested information, or completes a web form. Reward is sparse (binary success/fail per multi-step task) but unambiguous. Experiments show: 20% improvement in WebArena success rate after RLHF fine-tuning from human demonstrations vs. zero-shot GPT-4.

**Imitation learning + RLHF for coding agents:** SWE-bench training involved collecting human demonstrations of GitHub issue resolution, fine-tuning on those demonstrations (behavioral cloning), then using automated test suite pass/fail as the reward signal for RL. The automated test environment provides the dense, unambiguous reward signal that human labelers cannot provide at scale for coding tasks.

## Related
- [[transformer-architecture]] — The base LLM architecture that RLHF fine-tunes
- [[prompt-engineering]] — RLHF fundamentally changed what prompting can achieve; understanding RLHF informs better prompting
- [[machine-learning-fundamentals]] — Reinforcement learning theory; PPO; policy gradient methods
- [[llm-training-and-scaling-laws]] — RLHF sits atop pre-training; understanding pre-training is prerequisite
- [[retrieval-augmented-generation]] — RAG + RLHF are complementary: RLHF aligns behavior, RAG extends knowledge
- [[diffusion-models-and-image-generation]] — RLHF applied to image generation (DALL-E 3, Stable Diffusion fine-tuning via human feedback)
- [[ai-safety-and-alignment]] — RLHF is the primary current method for safety alignment; its limitations drive the safety research agenda
- [[dopamine-reward-systems-neuroscience]] — Biological reward learning: VTA dopamine, temporal-difference errors, and addiction as reward hacking
- [[cognitive-biases]] — Human rater biases (anchoring, availability, confirmation) pollute preference labels and reward model training
- [[game-theory-and-strategic-thinking]] — Mechanism design for incentive-compatible labeling; Nash equilibria in multi-agent preference settings
- [[prospect-theory-and-decision-making]] — How loss aversion and reference-point bias shape human preference judgments used in RLHF


### Saturday Cross-Disciplinary Synthesis: RLHF as Applied Behavioral Science

**Connection to Behavioral Psychology — Operant Conditioning at Scale:**
RLHF is, at a fundamental level, Skinnerian operant conditioning applied to neural networks. B.F. Skinner's finding that behavior is shaped by its consequences — reinforced behaviors increase in frequency, punished behaviors decrease — is implemented in RLHF through the reward model (encoding human preferences as a scalar reward signal) and policy optimization (RL training that maximizes expected reward). The connection is not merely metaphorical: RLHF training produces behavioral shaping effects identical to those Skinner documented, including the "skinner box" failure modes: reward hacking (the AI finds novel behaviors that maximize the reward signal without satisfying the underlying human intent), analogous to Skinner's experimental animals learning superstitious behaviors that preceded rewards. The variable-interval reinforcement schedule (human raters providing inconsistent feedback) produces the persistent, compulsive response patterns that behavioral psychology documents for this reward schedule — suggesting that RLHF training dynamics are partially governed by the same reinforcement schedules that produce compulsive behavior in animals.

**Connection to Labor Economics — Human Data Labelers and the AI Supply Chain:**
RLHF requires human raters to evaluate AI outputs — and this human labor market has emerged as a significant and underexamined part of the AI supply chain. The AI annotation industry employs approximately 5–6 million workers globally (Data Labeling Market Research Report, 2025) — predominantly in Kenya, Uganda, India, and Southeast Asia, earning $2–8/hour to evaluate outputs for violence, sexual content, and harmful instructions that AI models produce. TIME Magazine's January 2023 investigation documented Sama workers in Kenya earning below Kenyan minimum wage to label traumatizing content for OpenAI's content moderation systems. The labor economics: digital work platforms (Remotasks, Scale AI) are creating a global "data gig economy" with few protections, no career pathways, and systematic exposure to psychologically harmful content — raising the question of whether the productivity benefits of AI are being partially subsidized by exploitation of a global precariat of data workers.

**Connection to Game Theory — Alignment as Mechanism Design:**
The "alignment problem" in RLHF — ensuring that the AI's learned reward function matches true human values — is a mechanism design problem: how to structure the rating process so that rater behavior reveals true preferences rather than gaming the rating system. Current RLHF implementations face documented gaming failures: raters provide higher scores to outputs that are longer, more confident-sounding, and use formal language — regardless of accuracy — because these features superficially resemble good answers. The mechanism design solution is to make rating harder to game: constitutional AI (AI rates its own outputs against a fixed set of principles) reduces dependence on fallible human raters; process reward models (rating intermediate reasoning steps rather than final outputs) reduce the reward-hacking surface; adversarial testing of the reward model identifies gaming vulnerabilities before deployment.

**Updated Related Connections:**
- [[Psychology/dopamine-reward-systems-neuroscience]] — Operant conditioning as RLHF's biological precedent; variable reinforcement and compulsive response
- [[Psychology/game-theory-and-strategic-thinking]] — Mechanism design for alignment; reward model gaming as strategic behavior
- [[Finance/behavioral-finance-and-investor-psychology]] — RLHF reward shaping as analog to investor conditioning by market feedback

---

### June 2026 Vault Cross-Links: RLHF as the Bridge Between Human Values and Machine Behavior

**RLHF ↔ Psychology:** RLHF is applied operant conditioning at scale: the reward model learns to predict human preference signals (the "reinforcement"), and policy gradient optimization shapes model behavior toward the reward signal. The parallel to Skinnerian conditioning is direct — but with important differences: human trainers provide sparse, noisy rewards on a timescale incompatible with the rapid gradient updates required, creating a need for learned reward models as "proxies" for the true human preference. The reward hacking problem (model learns to maximize the proxy reward in ways that diverge from actual human preferences) is exactly Goodhart's Law: "When a measure becomes a target, it ceases to be a good measure." See [[behavioral-finance-and-investor-psychology]] for Goodhart's Law in institutional contexts and [[cognitive-biases]] for consistency bias in human feedback.

**RLHF ↔ Finance/Investment:** Reinforcement learning (the technical foundation of RLHF) is applied in algorithmic trading for dynamic strategy optimization. DeepMind's AlphaGo descendants have been adapted to portfolio rebalancing — RL agents trained to maximize Sharpe ratio over historical data have demonstrated out-of-sample performance improvements at firms including AQR and Two Sigma. The key challenge: financial markets are non-stationary (the environment changes continuously, and RL policies trained on historical data may not generalize to new regimes). Distributional RL (Bellemare et al., 2017) — modeling the full distribution of returns rather than just expected value — partially addresses this by maintaining uncertainty about the environment. See [[quantitative-finance-and-algorithmic-trading]] and [[Value-at-Risk-and-CVaR]].

**RLHF ↔ Geopolitics (autonomous weapons):** RLHF and standard RL are the primary training methodologies for autonomous weapons systems — targeting AI, navigation AI, and electronic warfare AI. The Ukraine conflict's widespread use of RL-trained drone navigation (avoiding GPS jamming through visual inertial odometry) demonstrates production deployment. The ethical concern addressed in [[moral-psychology-and-ethical-decision-making]]: RL-trained systems optimize for specified objective functions, which may not capture all morally relevant considerations — the "targeting AI" that maximizes target elimination rate has no intrinsic mechanism for valuing proportionality or civilian protection unless these are explicitly encoded in the reward function.

**New wikilinks:**
- [[cognitive-biases]] — consistency bias affecting human feedback quality
- [[behavioral-finance-and-investor-psychology]] — Goodhart's Law in institutional contexts
- [[quantitative-finance-and-algorithmic-trading]] — RL in algorithmic trading
- [[Value-at-Risk-and-CVaR]] — distributional RL and tail risk
- [[moral-psychology-and-ethical-decision-making]] — autonomous weapons reward function ethics
- [[agentic-ai-and-multi-agent-systems]] — RLHF in multi-agent coordination
- [[2026-06-06-russia-ukraine-summer-2026-deep-strikes-escalation]] — RL-trained autonomous drone navigation
