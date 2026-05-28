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

## Related
- [[prompt-engineering]]
- [[llm-landscape]]
- [[machine-learning-fundamentals]]
