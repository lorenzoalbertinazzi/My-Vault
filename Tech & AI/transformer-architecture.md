---
title: Transformer Architecture — How LLMs Work
date: 2026-05-26
tags: [tech, AI, LLM, transformer, deep-learning]
source: research
last_updated: 2026-05-26
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

## Related
- [[prompt-engineering]]
- [[llm-landscape]]
- [[machine-learning-fundamentals]]
