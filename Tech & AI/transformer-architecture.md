---
title: Transformer Architecture — How LLMs Work
date: 2026-05-26
tags: [tech, AI, LLM, transformer, deep-learning]
source: research
last_updated: 2026-05-28
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
