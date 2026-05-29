---
title: Transformer Architecture — How LLMs Work
date: 2026-05-26
tags: [tech, AI, LLM, transformer, deep-learning]
source: research
last_updated: 2026-05-29
---

## Summary
The transformer, introduced in "Attention Is All You Need" (Vaswani et al., 2017), is the architecture behind every major language model today. It replaced recurrent networks by processing all tokens in parallel using a mechanism called self-attention — which lets every token in a sequence look at every other token simultaneously. Understanding the transformer is the foundation for understanding GPT, Claude, Gemini, and every LLM in use.

## Key Points
- Transformers process entire sequences in parallel (vs. RNNs which process token by token)
- **Self-attention** lets each token attend to all other tokens — capturing long-range dependencies
- **Positional encoding** tells the model where each token sits in the sequence
- Modern LLMs stack dozens to hundreds of transformer layers
- Scale (more parameters, more data, more compute) reliably improves capability — the "scaling laws"

## Details

### The Input: Tokens and Embeddings
Text is split into **tokens** (roughly word-pieces: "transformer" → ["transform", "er"]). Each token is mapped to a high-dimensional vector (its **embedding**). GPT-4 uses ~100K token vocabulary; typical embedding dimension is 768–12,288 depending on model size.

**Positional encoding** is added to each embedding to give the model order information. Without it, "dog bites man" and "man bites dog" look identical.

### Self-Attention: The Core Mechanism
For each token, attention computes three vectors:
- **Query (Q)**: What this token is looking for
- **Key (K)**: What each token offers
- **Value (V)**: The actual content to pass forward

**Attention score** = softmax(QKᵀ / √d_k) × V

In plain English: each token votes on how much it should attend to every other token. High score = "this other token is very relevant to understanding me." The result is a weighted sum of all value vectors — a context-enriched representation.

**Multi-head attention**: Run attention multiple times in parallel with different learned Q/K/V projections. Each "head" specializes in different relationships (syntax, semantics, coreference, etc.). Typically 8–96 heads per layer.

### Feed-Forward Network (FFN)
After attention, each token passes through a position-wise FFN (two linear layers with a non-linearity). This is where much of the model's "memory" of facts lives — the FFN acts as a lookup table.

Rule of thumb: FFN has 4× the hidden dimension of the attention layer. In large models this is where most parameters reside.

### Layer Normalization and Residual Connections
Every sub-layer (attention + FFN) uses:
- **Residual connection**: Output = Input + SubLayer(Input). Enables training very deep networks (gradients flow back easily).
- **Layer norm**: Normalizes activations for training stability.

### The Full Stack: Encoder vs. Decoder

**Encoder-only (e.g., BERT)**: Sees full input sequence bidirectionally. Best for classification, NER, embeddings.

**Decoder-only (e.g., GPT, Claude, Llama)**: Autoregressive — predicts the next token, can only attend to past tokens (causal masking). The dominant architecture for LLMs.

**Encoder-Decoder (e.g., T5, original translation models)**: Encoder processes input, decoder generates output. Used in translation and summarization.

### Scaling Laws (Kaplan et al., OpenAI 2020)
Model performance (loss) follows predictable power laws with:
- **N**: Number of parameters
- **D**: Dataset size (tokens)
- **C**: Compute budget (FLOPs)

Key finding: to optimally use a compute budget, scale parameters and data together. Doubling compute → predictable improvement in loss. This gave labs confidence that bigger = better, driving the race to trillion-parameter models.

**Chinchilla scaling (Hoffmann et al., 2022)**: Earlier models were undertrained. Optimal ratio is ~20 tokens per parameter. A 70B model should train on ~1.4T tokens.

### Training: Pretraining and Fine-Tuning

**Pretraining**: Predict next token on a massive text corpus. The model learns language, facts, reasoning patterns, and world knowledge from pure prediction.

**Supervised Fine-Tuning (SFT)**: Train on curated (prompt, ideal response) pairs. Teaches the model to follow instructions.

**RLHF (Reinforcement Learning from Human Feedback)**: Human raters rank model outputs → train a reward model → use RL (PPO) to maximize reward. Aligns model behavior with human preferences. Used by GPT-4, Claude.

**DPO (Direct Preference Optimization)**: Simpler alternative to RLHF. Directly fine-tune on preference pairs without the separate reward model. Increasingly used because it's more stable.

### Inference: How Generation Works
At inference time, the model receives a prompt and generates tokens one at a time:
1. Run full forward pass on prompt → get probability distribution over next token
2. **Sample** (or argmax) from distribution → chosen token
3. Append token to context → repeat

**Temperature**: Controls randomness. T=0 → always pick highest probability token (deterministic). T=1 → sample from raw distribution. T>1 → more random.

**KV Cache**: Store computed Key/Value matrices for all previous tokens, so you don't recompute them each step. Critical for fast inference.

**Context window**: Maximum tokens the model can process at once. Determined by positional encoding and memory constraints. Current frontier models: 128K–1M+ tokens.

### Grouped-Query Attention (GQA) and Multi-Query Attention (MQA)

Standard multi-head attention uses N separate Key and Value projection matrices for N heads. During inference, the KV cache grows with both sequence length and number of heads — consuming huge memory for long-context generation.

**Multi-Query Attention (MQA)**: All query heads share a single K/V projection. Dramatically reduces KV cache memory (N× reduction) with modest quality loss.

**Grouped-Query Attention (GQA)**: Middle ground — groups of query heads share a single K/V projection. Llama 2/3, Mistral, Gemma all use GQA. Balances KV cache efficiency with quality.

**Why this matters for inference speed**: With a large KV cache, memory bandwidth becomes the bottleneck (not compute). GQA enables longer contexts and larger batches on the same hardware.

---

### RoPE: Rotary Position Embedding

Standard absolute positional embeddings (sinusoidal or learned) struggle to generalize to sequences longer than the training context. **RoPE** (Su et al., 2021) applies rotation matrices to query and key vectors such that attention scores depend on the *relative distance* between tokens, not their absolute positions.

**Key advantages**:
- Long-context generalization: trained at 4K context, can extrapolate to 32K+ with techniques like YaRN
- Relative position is encoded implicitly in the attention scores — the model "automatically" sees the gap between any two tokens
- Used in Llama, Mistral, PaLM, Falcon, and most modern open-source models

---

### Flash Attention

The naive attention algorithm requires O(N²) memory to store the attention matrix (where N = sequence length). For N=100K tokens, this is 100B entries — impossible in GPU memory.

**Flash Attention** (Dao et al., 2022) and **Flash Attention 2/3** reorder the computation so the full attention matrix is never materialized. Instead, it computes attention in tiles that fit in GPU SRAM (fast, small), streaming through them. The result:
- **Memory**: O(N) instead of O(N²)
- **Speed**: 2–4× faster than standard attention due to fewer HBM (slow) memory reads
- **Same mathematical output**: Flash Attention is exact, not approximate

Flash Attention is now standard in virtually all serious transformer implementations. It is the primary reason long-context (100K+) inference is feasible.

---

### Speculative Decoding

LLM inference is memory-bandwidth bound: generating each token requires reading all model weights from GPU memory. **Speculative decoding** (Chen et al., 2023) dramatically speeds this up:

1. A small "draft" model (e.g., 7B) generates N candidate tokens quickly
2. The large "target" model verifies all N tokens in a *single* forward pass (parallel verification is possible because all candidate tokens are known)
3. If the large model agrees with the small model's tokens, all N are accepted; if it disagrees at token k, all tokens after k are rejected and regenerated

**Result**: Effectively 2–3× generation speedup with no quality loss (the output distribution is identical to the large model alone). Used in production systems at Google, Anthropic, and others.

---

### Sparse Attention: Efficient Long-Context Processing

Full self-attention is O(N²) in sequence length. Several sparse attention patterns reduce this:

**Longformer** (Beltagy et al., 2020): Combines local sliding-window attention (each token attends to a fixed neighborhood) with global attention (certain "global" tokens attend to the full sequence). O(N×window_size) rather than O(N²). Effective for documents.

**BigBird** (Google, 2020): Adds random attention (each token attends to a random subset) to the Longformer pattern. Theoretically proven to be a universal approximator.

**Sliding window + global attention** is now widely used in production models for document processing tasks that don't require full pairwise attention.

### State Space Models — The Transformer Alternative

Transformers have a fundamental scaling problem: self-attention is O(N²) in sequence length. For very long sequences (100K+ tokens), this becomes prohibitively expensive. **State Space Models (SSMs)** offer an alternative with O(N) scaling.

**The core idea**: SSMs model sequences using a hidden state that is updated at each step, similar to an RNN — but with a structured mathematical form that allows parallel training (unlike RNNs which must process sequentially).

**Mamba** (Gu & Dao, 2023) is the leading SSM architecture. Its key innovation: **selective state spaces** — the state update parameters are input-dependent, allowing the model to selectively remember or forget information based on content (not just position). This gives it associative recall capabilities closer to attention than prior SSMs.

| Property | Transformer | Mamba (SSM) |
|----------|------------|-------------|
| Training complexity | O(N²) attention | O(N) with parallel scan |
| Inference complexity | O(N) per token (with KV cache) | O(1) per token (fixed hidden state) |
| Memory at inference | Grows with context length (KV cache) | Fixed — state size constant |
| Long-context ability | Degrades (lost in the middle) | Maintains quality at very long sequences |
| Associative recall | Excellent (direct attention) | Weaker than transformers on some retrieval tasks |

**RWKV** (Peng et al., 2023): Another linear-attention alternative that formulates attention as an RNN at inference time. Can be trained as a transformer (parallel) but runs as an RNN (O(1) memory). The RWKV-7 model demonstrates competitive performance with transformer models of similar scale.

**The hybrid approach**: Pure SSM models underperform transformers on some recall-intensive tasks. **Jamba** (AI21 Labs), **Zamba**, and **Falcon Mamba** use interleaved transformer and Mamba layers — capturing long-range dependencies efficiently while maintaining strong associative recall. This hybrid architecture is likely the direction for next-generation frontier models.

---

### Mixture of Depths (MoD)

Standard transformers apply the same amount of computation to every token at every layer. Most tokens are simple (stop words, punctuation, common phrases) and don't benefit from full computation.

**Mixture of Depths** (Raposo et al., 2024) makes compute allocation adaptive:
- A learned router at each layer decides which tokens "participate" in the full computation of that layer
- Tokens deemed unimportant are passed through via a residual connection (skip this layer's attention + FFN)
- A fixed fraction of tokens (e.g., 12.5%) participate at each layer; the rest skip

**Result**: Same quality as a standard transformer at 50–70% of the FLOPs, because compute is concentrated where it matters most. MoD complements Mixture of Experts (MoE) — MoE routes tokens to different experts horizontally (different FFN networks at the same layer); MoD routes tokens vertically (skip entire layers).

**Combined MoE + MoD**: Route both which experts process a token AND which layers process it. This is the frontier of compute-efficient transformer design as of 2026.

---

### The "Lost in the Middle" Phenomenon

A critical practical limitation of long-context models: **LLMs systematically perform worse on information positioned in the middle of a long context window**, even though the same information at the beginning or end is retrieved accurately.

**The finding** (Liu et al., 2023, Stanford): When relevant information is buried in the middle of a 20-document context, model performance drops 30–50% relative to when the same information is at the start or end. This holds across GPT-3.5, GPT-4, and Claude.

**Why it happens**: Attention distributions show U-shaped recency/primacy bias — models attend most to the beginning (system prompt, first few documents) and end (most recent tokens) of the context. Middle content is attended to proportionally less.

**Practical mitigations**:
- **Position important content at the start and end** of the context window
- **Retrieve fewer, more relevant chunks** rather than many weakly-relevant chunks (reduce needle-in-haystack problem)
- **Reorder retrieved chunks** so the most relevant appear first and last
- **Use long-context models designed to mitigate this** (Gemini 1.5's "needle in a haystack" benchmarks were specifically designed to test for this; their sliding attention patterns partially mitigate it)
- **Post-retrieval compression**: use a re-ranking step to reduce the number of chunks before generation

This phenomenon means that naive "stuff everything in the context window" approaches degrade as context grows — RAG with targeted retrieval often outperforms long-context ingestion for specific factual queries even when the full document fits.

---

### Historical Development

The transformer did not emerge from a vacuum — it was the culmination of three decades of sequence modelling research, each phase solving one limitation of the previous architecture.

**1986 — Backpropagation and the RNN**: Rumelhart, Hinton & Williams formalise backpropagation through time (BPTT), making it theoretically possible to train Recurrent Neural Networks (RNNs) on sequential data. RNNs process sequences token-by-token, passing a hidden state forward — but gradients vanish over long sequences, preventing the model from learning long-range dependencies.

**1997 — LSTM (Long Short-Term Memory)**: Hochreiter & Schmidhuber (Technische Universität München) introduce gated memory cells — forget gate, input gate, output gate — that allow gradients to flow across hundreds of time steps. LSTMs become the dominant sequence architecture for the next 20 years, powering Google Translate (launched 2016), Apple's Siri neural network backend, and Amazon Alexa.

**2014 — Neural Attention**: Dzmitry Bahdanau, Kyunghyun Cho, and Yoshua Bengio (Université de Montréal) publish "Neural Machine Translation by Jointly Learning to Align and Translate." They add an attention mechanism to an encoder-decoder RNN, allowing the decoder to look back at all encoder states rather than relying on a single compressed context vector. BLEU scores on English→French translation improve by ~4 points — a massive jump. The paper introduces the query/key/value framing that the 2017 transformer will formalise.

**2015 — Sequence-to-Sequence with Attention (Google Brain)**: Vinyals, Le et al. demonstrate that attention-augmented RNNs can handle variable-length output sequences. The architecture dominates machine translation, summarisation, and question answering benchmarks through 2017.

**June 2017 — "Attention Is All You Need"**: Eight authors at Google Brain and Google Research — Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan Gomez, Łukasz Kaiser, and Illia Polosukhin — publish the transformer. The key insight: discard the RNN entirely. Replace sequential processing with parallel self-attention over the full sequence. On WMT 2014 English-to-German translation, the transformer achieves 28.4 BLEU — surpassing all prior models at a fraction of the training cost (12 hours on 8 P100 GPUs vs. weeks for the best RNN ensemble). The paper is presented at NeurIPS 2017 and becomes the most-cited machine learning paper in history, with over 100,000 citations by 2026.

**June 2018 — GPT-1 (OpenAI)**: Alec Radford, Karthik Narasimhan, Tim Salimans, and Ilya Sutskever train a 117M-parameter decoder-only transformer on BooksCorpus (7,000 books, ~1B words). Fine-tuned on downstream tasks with only small datasets, GPT-1 outperforms task-specific models — establishing the "pre-train on general text, fine-tune on task" paradigm.

**October 2018 — BERT (Google)**: Jacob Devlin, Ming-Wei Chang, Kenton Lee, and Kristina Toutanova train a 340M-parameter encoder-only transformer with bidirectional masked language modelling (MLM). BERT achieves state-of-the-art on 11 NLP benchmarks simultaneously, including the GLUE benchmark (80.5 vs. previous SOTA of 72.1 — an 8-point jump). BERT demonstrates that the same architecture, simply pretrained differently, can serve both generation and understanding tasks.

**February 2019 — GPT-2 (OpenAI, 1.5B parameters)**: Trained on WebText (40GB of Reddit outlinks, ~8M documents). OpenAI stages the release over 9 months citing "misuse concerns" — the first major AI safety-motivated staged release, igniting a public debate about responsible disclosure. GPT-2 produces remarkably coherent long-form text but is still unreliable for factual tasks.

**May 2020 — GPT-3 (OpenAI, 175B parameters)**: Brown et al. train on ~500B tokens (Common Crawl, Books1, Books2, Wikipedia). Crucially, GPT-3 demonstrates **few-shot learning** at scale: given 3–5 examples in the prompt, it performs competitively with fine-tuned models on many tasks. This eliminates the need for task-specific training for the first time. Training cost: approximately $4.6M in cloud compute.

**2020 — Scaling Laws (Kaplan et al.)**: OpenAI publishes the first systematic scaling laws paper, quantifying power-law relationships between model size, data, compute, and loss. This gives the field a roadmap: performance is predictable.

**November 2022 — ChatGPT / InstructGPT**: OpenAI releases ChatGPT (RLHF-trained GPT-3.5), achieving 1 million users in 5 days and 100 million in 2 months — the fastest-growing consumer application in history. RLHF transforms the research architecture into a product, demonstrating that alignment training is the critical last mile.

**March 2023 — GPT-4 (OpenAI)**: Multimodal (vision + language), achieves 90th percentile on the Uniform Bar Exam, 88th percentile on LSAT. Estimated 1 trillion parameters with Mixture of Experts architecture (never confirmed by OpenAI).

**2023–2026 — Open-Source and Competition**: Meta releases Llama (February 2023, 65B), then Llama 2 (July 2023), then Llama 3 (April 2024, 70B/405B). Mistral AI (founded by former DeepMind/Meta researchers, Paris, June 2023) releases Mistral 7B and Mixtral 8x7B (MoE). Google releases Gemini Ultra (December 2023), Gemma (February 2024). Anthropic releases Claude 3 Opus/Sonnet/Haiku (March 2024). DeepSeek (Hangzhou, China) releases DeepSeek-V3 (December 2024) and R1 (January 2025) — matching GPT-4-class performance at dramatically lower training cost (~$6M claimed), reshaping market expectations about compute requirements.

---

### Mathematical Foundation

#### The Full Attention Computation

Let the input sequence have length *N* and each token be represented by a *d_model*-dimensional vector. The input matrix **X** ∈ ℝ^(N × d_model).

For one attention head with key dimension *d_k*:

```
Q = X · W_Q    (query projection)     W_Q ∈ ℝ^(d_model × d_k)
K = X · W_K    (key projection)       W_K ∈ ℝ^(d_model × d_k)
V = X · W_V    (value projection)     W_V ∈ ℝ^(d_model × d_v)

Attention(Q, K, V) = softmax(Q·Kᵀ / √d_k) · V
```

**The √d_k scaling factor**: Without this, for large *d_k*, the dot products Q·Kᵀ grow large in magnitude, pushing softmax into regions of near-zero gradient (the "flat" tails of the softmax function), making training unstable. Dividing by √d_k keeps the variance of the dot product roughly 1.0 regardless of *d_k*.

**The softmax**: For attention row *i*, the unnormalised scores are **a**_i = Q_i · Kᵀ / √d_k. The softmax converts these to normalised weights:

```
softmax(a_i)_j = exp(a_{ij}) / Σ_k exp(a_{ik})
```

This ensures each row of the attention matrix sums to 1.0 — the output is a weighted average of the value vectors.

**Multi-head attention** with *h* heads, each with d_k = d_v = d_model/h:

```
MultiHead(Q, K, V) = Concat(head_1, ..., head_h) · W_O

where head_i = Attention(Q·W_Qi, K·W_Ki, V·W_Vi)

W_O ∈ ℝ^(h·d_v × d_model)  [output projection to restore d_model dimensions]
```

**FLOP count for a single forward pass** through a transformer with *L* layers, *N* sequence length, *d_model* embedding dimension, *h* heads, and FFN hidden dimension *d_ffn* = 4·d_model:

- Attention QKV projections: 3 × 2·N·d_model² per layer = 6N·d_model²·L
- Attention matrix (Q·Kᵀ): 2·N²·d_k = 2N²·d_model per layer (L layers) = 2N²·d_model·L
- FFN: 2 × 2·N·d_model·d_ffn·L = 16N·d_model²·L

**Total ≈ 24N·d_model²·L** FLOPs per forward pass (for N << d_model), dominated by the matrix multiplications. Training requires ~6 FLOPs per parameter per token (2 forward pass, 4 backward pass). GPT-3's training compute: 175B params × 300B tokens × 6 ≈ 3.14 × 10²³ FLOP.

#### Causal Masking

Decoder-only models prevent each token from attending to future tokens by setting attention scores to -∞ before the softmax for the upper triangle of the attention matrix:

```
score_{ij} = { Q_i · K_j / √d_k    if j ≤ i
             { -∞                    if j > i
```

After softmax, -∞ becomes 0.0 — the token attends only to its context.

#### Layer Normalisation (Pre-Norm vs. Post-Norm)

**Post-Norm** (original 2017 paper): LayerNorm applied *after* residual connection:
```
x' = LayerNorm(x + Sublayer(x))
```
Requires careful learning rate warmup to train stably.

**Pre-Norm** (GPT-2, Llama, all modern models): LayerNorm applied *before* the sublayer:
```
x' = x + Sublayer(LayerNorm(x))
```
More stable to train (gradient flows cleanly through the residual), works with higher learning rates. The residual connection ensures x' ≈ x at initialisation — the sublayer learns a small residual correction.

**SwiGLU FFN** (Noam Shazeer, 2020; used in Llama, PaLM):
```
FFN_SwiGLU(x) = (SiLU(W₁x) ⊙ W₂x) · W₃
```
Uses two separate linear projections in the first layer, with elementwise gating. Empirically outperforms vanilla ReLU FFN by ~0.5-1.0 perplexity points at equal parameter count.

---

### Benchmarks and Performance

The following tables document capability milestones with specific numbers, providing a quantitative record of the transformer era.

#### Language Understanding and Reasoning (MMLU — Massive Multitask Language Understanding)
MMLU (Hendrycks et al., 2020) tests 57 subjects from elementary mathematics to professional law and medicine. Random baseline: 25%. Human expert: ~89%.

| Model | Release | MMLU (5-shot) | Notes |
|-------|---------|--------------|-------|
| GPT-3 (175B) | May 2020 | 43.9% | First large-scale demonstration of few-shot learning |
| Gopher (280B, DeepMind) | Dec 2021 | 60.0% | Scaling LLMs beyond GPT-3 |
| Chinchilla (70B, DeepMind) | Mar 2022 | 67.5% | Smaller model, optimal training data ratio |
| GPT-4 | Mar 2023 | 86.4% | First model approaching human expert range |
| Claude 3 Opus | Mar 2024 | 86.8% | Competitive with GPT-4 |
| Gemini Ultra | Dec 2023 | 90.0% | First model exceeding human expert baseline |
| GPT-4o | May 2024 | 88.7% | Multimodal at GPT-4 quality |
| Claude 3.5 Sonnet | Jun 2024 | 88.7% | Matches GPT-4o at lower inference cost |
| DeepSeek-V3 | Dec 2024 | 88.5% | Chinese frontier model at dramatically lower training cost |
| o3 (OpenAI) | Apr 2025 | 91.6% | Extended thinking model |

#### Mathematical Reasoning (MATH — Hendrycks et al.)
MATH contains 12,500 competition math problems (AMC, AIME level). Human expert: ~90%.

| Model | MATH Score | Notes |
|-------|-----------|-------|
| GPT-4 (standard) | 52.0% | 2023 baseline |
| Gemini Ultra | 53.2% | Comparable to GPT-4 |
| GPT-4o | 76.6% | Major improvement via training refinements |
| o1 (OpenAI) | 94.2% | Extended thinking; 2024 |
| DeepSeek-R1 | 97.3% | Extended thinking; January 2025 |
| o3 | 96.7% | Top-tier as of 2025 |

#### Code Generation (HumanEval — pass@1)
HumanEval (Chen et al., 2021): 164 Python programming problems. Tests basic algorithmic implementation.

| Model | HumanEval pass@1 | Notes |
|-------|-----------------|-------|
| GPT-3 (few-shot) | 11.4% | Codex paper baseline |
| Codex (12B, 2021) | 28.8% | Fine-tuned on GitHub code |
| GPT-4 | 67.0% | 2023 |
| Claude 3 Opus | 84.9% | 2024 |
| Claude 3.5 Sonnet | 92.0% | Mid-2024; near-human ceiling on easy problems |
| GPT-4o | 90.2% | 2024 |

#### Long-Context Retrieval (Needle in a Haystack)
This test embeds a specific fact ("The best thing to do in San Francisco is eat a sandwich") at various positions within contexts of up to 1M tokens, then queries for it. Gemini 1.5 Pro (February 2024) was the first model to achieve near-perfect (~99%) recall across all positions in a 1M-token context.

#### Inference Efficiency
- **KV Cache memory**: For Llama 3 70B with GQA (8 KV heads), 128K context, bf16 precision: KV cache = 2 × 8 × 128,000 × 8,192 / 8 (GQA reduction) × 2 bytes ≈ 33 GB. Without GQA (standard MHA): 264 GB — the difference between fitting on 4× A100s vs. requiring 16×.
- **Speculative decoding speedup**: 2–3× throughput improvement with a draft model of ~1/10th the target model's size (e.g., 7B draft for 70B target), with zero quality loss (mathematically identical output distribution).

---

### Current Research Frontier

**Long-Context Scaling**: Gemini 1.5 Pro demonstrated 1M-token context; Google's 2025 models support 2M tokens. The key research question is whether models with very long contexts can maintain genuine reasoning quality (not just retrieval) across the full window. Current evidence suggests they cannot yet — the "lost in the middle" effect persists even at 1M tokens.

**Multimodality as First-Class**: GPT-4o, Gemini 1.5/2, Claude 3/3.5/4 are natively multimodal — trained on interleaved text, image, audio, and video tokens from the start rather than patching modalities onto a text model. The research frontier is video understanding with long temporal reasoning (hours of video, not seconds).

**Test-Time Compute Scaling**: OpenAI o1/o3, DeepSeek-R1, and Claude's extended thinking mode all demonstrate that allowing a model to generate internal reasoning tokens before answering can match or exceed the capability gains of training a model 10× larger. The current research questions are: how to train optimal thinking patterns (process reward models vs. MCTS vs. policy gradient), how to efficiently route queries to the appropriate thinking budget, and whether there is a ceiling to test-time scaling.

**Architecture Hybridisation**: Pure transformer vs. pure SSM (Mamba) is giving way to hybrid architectures. Jamba (AI21 Labs, 2024) interleaves transformer and Mamba layers 1:7. The 2026 research direction is principled layer-type assignment — understanding which layers in a transformer benefit from full attention vs. linear attention vs. SSM, enabling architecturally-optimal hybrid designs.

**Post-Training Alignment at Frontier Scale**: RLHF with PPO has been largely displaced by DPO (Direct Preference Optimisation) and its variants (ORPO, SimPO, IPO) for computational efficiency reasons. The active research frontier is scalable oversight — how to maintain alignment as models become more capable than human raters at the tasks being evaluated. Constitutional AI (Anthropic), debate (DeepMind), and process reward models are the leading approaches.

## Cross-Disciplinary Connections

### Transformers and the Economics of Intelligence

The transformer's scaling laws — the empirical finding that model performance improves predictably as a power-law function of parameters, data, and compute — resemble nothing so much as the production functions studied in macroeconomics. Just as economists describe output as a function of capital and labor inputs, Kaplan's scaling laws describe model quality as a function of compute capital. This analogy is more than superficial: the race to train ever-larger models has driven capital expenditures measured in the billions of dollars, reshaping the economics of AI development in a way that strongly favors incumbents with access to large GPU clusters. The barrier to entry created by these capital requirements mirrors classic industrial economics — it is not unlike the capital moats that protect semiconductor fabs or oil refineries. This structural dynamic has profound implications for the US-China great power competition explored in [[2026-05-27-us-china-great-power-competition]], where access to leading-edge AI chips has become a first-order strategic variable, and the US export controls on NVIDIA H100/A100 GPUs represent an attempt to constrain an adversary's ability to scale on the same curve.

### Attention, Cognition, and Cognitive Bias

The self-attention mechanism is, at its core, a learned salience function: for any given token, it computes which other tokens are most relevant to its interpretation. This mirrors well-established theories of human selective attention in cognitive psychology. Both biological and artificial attention systems face the same fundamental tradeoff between breadth (attending to many things weakly) and depth (attending to few things strongly). The "lost in the middle" phenomenon — where transformers systematically underweight information positioned in the middle of a long context — is directly analogous to the primacy and recency effects documented in human memory research, subjects in psychological experiments consistently recall the first and last items in a list better than middle items. The mechanism differs (transformers show U-shaped attention distributions due to positional encoding and training dynamics; humans show serial position effects due to working memory constraints) but the behavioral signature is identical. This is not coincidental: both systems are trained on sequential data and must develop representations that are useful for predicting what comes next, and that training pressure biases both toward attending to contextual boundaries. The [[cognitive-biases]] note covers primacy/recency effects in human judgment, and practitioners building RAG systems would benefit from understanding both the human and computational versions of the same phenomenon — both argue for placing critical information at the beginning and end of any sequence.

### Transformers as Infrastructure for the Entire AI Stack

Understanding the transformer architecture is prerequisite to understanding the entire downstream technology stack in this vault. The self-attention mechanism is directly responsible for the quality of vector embeddings used in [[vector-databases-and-embeddings]]: encoder-only transformers (BERT, E5, BGE) produce the dense semantic representations that make similarity search meaningful. Without the transformer's ability to capture long-range contextual relationships, an embedding of "bank" could not distinguish between the financial institution and the riverbank. The quality of retrieval in [[retrieval-augmented-generation]] pipelines is therefore fundamentally bottlenecked by the expressiveness of transformer-generated embeddings. Similarly, the prompt engineering techniques in [[prompt-engineering]] are most precisely understood as techniques for manipulating the probability distribution over next tokens — chain-of-thought prompting works because intermediate reasoning tokens provide context that dramatically shifts the probability distribution toward correct downstream tokens, exploiting the transformer's ability to condition generation on arbitrarily long prior context.

### Scaling Laws, Finance, and the Limits of Extrapolation

The investor psychology literature in [[behavioral-finance-and-investor-psychology]] describes how investors systematically over-extrapolate recent trends — the gambler's fallacy in reverse, assuming that recent strong performance predicts future performance. The AI industry's relationship to scaling laws has exhibited exactly this pattern. The smooth power-law improvements from 2018 to 2022 led to widespread belief that scaling alone would produce artificial general intelligence on a predictable timeline, an assumption priced into the valuations of AI companies. The emergence of phenomena like "emergent capabilities" — where new abilities appear discontinuously at scale thresholds — and the potential flattening of returns from pretraining data exhaustion represent exactly the kind of regime-change risk that trend extrapolators miss. The Chinchilla scaling paper itself was a correction to over-extrapolation: earlier models were undertrained relative to their parameter count because researchers were extrapolating the wrong scaling curve. For investors evaluating AI companies, this history argues strongly against naive trend extrapolation — the same cognitive error that drives momentum investing also shapes AI hype cycles.

### State Space Models and Mean-Reversion in Technology

The emergence of Mamba and state space models as transformer alternatives illustrates a broader pattern in technology cycles: apparent monocultures are always challenged by architectures that address the dominant architecture's structural weaknesses. The transformer's O(N²) attention complexity makes it fundamentally expensive for very long contexts; SSMs address this but sacrifice associative recall quality. This tradeoff structure — incumbents with superior performance on known benchmarks, challengers with superior efficiency on new task dimensions — resembles the dynamics of market competition analyzed in [[value-investing-methodology]], where moats erode precisely at the boundaries of existing strengths. The hybrid Jamba/Zamba architectures (interleaving transformer and Mamba layers) represent the technological equivalent of conglomerate strategies: capturing complementary strengths while accepting the management complexity of maintaining two distinct operating models.

## Related
- [[prompt-engineering]]
- [[machine-learning-fundamentals]]
- [[vector-databases-and-embeddings]]
- [[retrieval-augmented-generation]]
- [[diffusion-models-and-image-generation]]
- [[2026-05-27-us-china-great-power-competition]]
- [[cognitive-biases]]
- [[behavioral-finance-and-investor-psychology]]
- [[value-investing-methodology]]
