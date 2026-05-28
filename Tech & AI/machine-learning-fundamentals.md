---
title: Machine Learning Fundamentals
date: 2026-05-26
tags: [tech, AI, machine-learning, neural-networks, deep-learning]
source: research
last_updated: 2026-05-28
---

## Summary
Machine learning is the field of building systems that learn from data rather than being explicitly programmed. Understanding the fundamentals — types of learning, how neural networks train, and the bias-variance tradeoff — gives you the conceptual foundation to understand any ML system, from simple regressions to modern LLMs.

## Key Points
- ML systems are optimized by minimizing a **loss function** via **gradient descent**
- **Overfitting** (too complex) and **underfitting** (too simple) are the core tradeoff
- Neural networks are universal function approximators — they can learn any mapping given enough data
- **Backpropagation** efficiently computes gradients through deep networks
- The choice of architecture, loss function, and optimizer is the core of ML system design

## Details

### Types of Machine Learning

**Supervised Learning**: Learn a mapping from inputs X to outputs Y from labeled examples. The model minimizes prediction error on training data.
- Regression: continuous output (predict house price)
- Classification: discrete output (spam/not spam)

**Unsupervised Learning**: Find structure in unlabeled data.
- Clustering (K-means): group similar examples
- Dimensionality reduction (PCA, autoencoders): compress data to its essential structure

**Reinforcement Learning (RL)**: An agent takes actions in an environment, receives rewards, and learns to maximize cumulative reward. No labeled data — learns from consequences.
- Used in: game playing (AlphaGo), robotics, RLHF for LLMs

**Self-Supervised Learning**: Generate labels from the data itself. Pretraining LLMs (predict next token) is self-supervised — no human labeling required. The key to scaling.

### Neural Networks

A neural network is a composition of **linear transformations** followed by **non-linear activation functions**:

```
Layer output = Activation(Weight × Input + Bias)
```

Stack many layers → **deep network**. The depth allows learning hierarchical features:
- Layer 1: edges and textures (images) / syntax patterns (text)
- Layer N: abstract concepts (faces, semantic meaning)

**Activation functions**:
- **ReLU** (max(0, x)): Most common. Fast, avoids vanishing gradients.
- **Sigmoid** (1/(1+e⁻ˣ)): Squashes to [0,1]. Used in output layer for binary classification.
- **Softmax**: Converts logits to probabilities summing to 1. Used for multi-class output.
- **GELU**: Smooth ReLU variant. Used in transformers (GPT, BERT).

### Training: Gradient Descent
The training loop:
1. **Forward pass**: Run input through network → get prediction
2. **Compute loss**: How wrong was the prediction? (e.g., cross-entropy for classification, MSE for regression)
3. **Backward pass (backpropagation)**: Compute gradient of loss with respect to every parameter using the chain rule
4. **Update parameters**: Move each parameter in the direction that reduces loss

**Learning rate**: How big each step is. Too large → diverges. Too small → too slow. **Learning rate scheduling** (warm-up, cosine decay) is critical for training large models.

**Optimizers**:
- **SGD**: Basic stochastic gradient descent
- **Adam**: Adaptive learning rates per parameter + momentum. The default for most deep learning.
- **AdamW**: Adam with weight decay. Standard for training transformers.

### The Bias-Variance Tradeoff

**Bias**: Error from wrong assumptions (model too simple). Underfitting — fails on training AND test data.

**Variance**: Error from sensitivity to training data noise (model too complex). Overfitting — great on training data, fails on test data.

The sweet spot: a model complex enough to capture real patterns, but not so complex it memorizes noise.

**Regularization techniques** to control variance:
- **Dropout**: Randomly zero out neurons during training → forces redundant representations
- **Weight decay (L2)**: Penalizes large weights → simpler models
- **Data augmentation**: Artificially expand training data → harder to overfit
- **Early stopping**: Stop training when validation loss stops improving

### Key Concepts for Deep Learning

**Batch size**: How many examples to use per gradient update. Larger = more stable gradients, more memory. Smaller = noisy gradients (can help escape local minima).

**Epochs**: One pass through the entire training dataset. Models train for many epochs.

**Train / Validation / Test split**: Train on training set. Tune hyperparameters on validation set. Report final performance on test set (only look at this once — looking multiple times leaks information).

**Transfer learning**: Take a model pretrained on a large dataset, fine-tune it on your specific task. You inherit the general representations learned at scale. This is why fine-tuning a pretrained LLM beats training from scratch on a small dataset by orders of magnitude.

### Loss Functions

| Task | Loss Function | Formula |
|------|--------------|---------|
| Binary classification | Binary cross-entropy | −(y log p + (1−y) log(1−p)) |
| Multi-class | Categorical cross-entropy | −Σ yᵢ log(pᵢ) |
| Regression | Mean Squared Error (MSE) | Σ(y − ŷ)² / n |
| LLM next-token prediction | Cross-entropy | −log P(correct token) |

### Evaluation Metrics
- **Accuracy**: Fraction correct. Misleading with imbalanced classes.
- **Precision**: Of predicted positives, how many were real? (TP / (TP + FP))
- **Recall**: Of real positives, how many did we find? (TP / (TP + FN))
- **F1 Score**: Harmonic mean of precision and recall. Good for imbalanced problems.
- **AUC-ROC**: Discrimination ability across all classification thresholds. Best for binary classifiers.
- **Perplexity**: Language model metric. Lower = better. How "surprised" the model is by the test data.

### Mixture of Experts (MoE)

Standard ("dense") transformers pass every input token through every parameter on every forward pass. This is computationally expensive at scale.

**MoE** replaces some FFN layers with a set of "expert" sub-networks, plus a learned router that selects which experts to activate for each token:

- A model might have 64 expert FFNs but only activate 2 per token
- Total parameters are large (e.g., 1.7T) but computation per token is ~1/32 of what a dense model of that size would require
- Different experts specialize in different patterns — some handle code, others factual recall, others reasoning

**GPT-4, Gemini 1.5, Mixtral 8x7B, and Grok** all use MoE. The architecture explains why the largest modern models can have enormous parameter counts without proportionally more compute per token.

**Challenge**: Load balancing — if all tokens route to the same few experts, others are wasted. Auxiliary "load balancing loss" encourages even distribution.

---

### Knowledge Distillation

Training large models is expensive; deploying them is slow. **Knowledge distillation** transfers knowledge from a large "teacher" model to a small "student" model:

1. The teacher generates **soft probability distributions** (not just the top class, but probabilities across all classes)
2. The student is trained to match these distributions, not the hard labels
3. Soft probabilities contain information about relative similarity between classes — the teacher's knowledge is richer than just "the answer"

**Result**: A small student model often substantially outperforms a model of the same size trained from scratch. Many deployed models (DistilBERT at 60% of BERT's size but 97% of performance; TinyLlama; Phi-3) are distilled from larger teachers.

---

### Model Compression: Quantization and Pruning

**Quantization**: Reduce numerical precision of model weights:
- Full precision: float32 (32 bits per weight)
- Half precision: float16 / bfloat16 (16 bits) — ~2× memory reduction with minimal quality loss
- int8 quantization: 8 bits — ~4× reduction, modest quality loss
- 4-bit quantization (GGUF, AWQ, GPTQ): ~8× reduction — enables 70B models to run on consumer GPUs

**Why it works**: Most model weights are nearly zero; most information is in a small fraction. Quantization sacrifices representation precision for memory and speed, with surprisingly small accuracy loss when done carefully.

**Pruning**: Remove entire neurons, attention heads, or layers that contribute little to outputs. Can reduce model size 30–50% with careful structured pruning + fine-tuning.

---

### Constitutional AI (Anthropic)

**Constitutional AI** (Bai et al., 2022) is a method for training AI systems to follow a set of principles ("constitution") while reducing the need for human feedback on harmful outputs:

1. **SL-CAI**: Generate responses, then have the model critique its own response using the constitution ("Does this response support autonomy? Is it honest?"), revise, and train on the revised pairs
2. **RL-CAI**: Use the AI itself (not human raters) to rank responses on principle adherence for the RLHF reward model

**Advantages**: Scales safely without requiring human raters to see harmful content; makes AI values transparent and auditable via the written constitution; allows systematic value updates by editing the constitution rather than retraining.

This approach is used in Claude's training at Anthropic and represents a significant methodological alternative to pure human-feedback RLHF.

---

### The Scaling Law Debate: When Does Bigger Stop Helping?

The original Kaplan scaling laws predicted smooth, predictable improvement with more compute. But several phenomena complicate this:

**Emergent abilities**: Certain capabilities (multi-step arithmetic, code generation, analogical reasoning) appear suddenly at specific scale thresholds — they are essentially absent below the threshold and present above it. This is not predicted by smooth scaling laws and creates genuine uncertainty about what larger models will be able to do.

**Diminishing returns vs. new capabilities**: Larger models get better at known tasks more slowly, but may unlock qualitatively new task types. The frontier is not just doing the same things better.

**Data quality wall**: Post-2023, pre-training datasets approach exhaustion of high-quality text. Training on synthetic data (generated by existing models) and multi-modal data (images, video, audio) may be necessary to continue scaling beyond current limits. Chinchilla scaling assumed fixed, clean data — that assumption is increasingly strained.

### Test-Time Compute — Inference Scaling as the New Frontier

The 2024–2026 period introduced a major paradigm shift: rather than only scaling training compute to improve models, **scaling inference compute** (letting the model "think longer" before answering) provides significant and predictable quality gains.

**The key insight** (OpenAI o1, DeepSeek-R1, Google Gemini 2.0 Flash Thinking): Models can solve harder problems if given more "thinking budget" at inference time — generating long chains of reasoning, backtracking when stuck, trying multiple approaches, and self-checking before outputting a final answer.

**Why it works**: Chain-of-thought tokens are not just output — they are *computation*. When a model generates a partial solution step-by-step, each generated token creates context that makes the next step easier to compute correctly. The model is effectively using its own token stream as working memory.

**Compute tradeoffs**: Test-time compute is expensive (more tokens = more cost). Optimal allocation depends on task difficulty — simple queries should use minimal compute; hard mathematical proofs or long-range code generation benefit from extended thinking. Models like o3 (OpenAI) and Claude 3.7 Sonnet (Anthropic) allow users to set "thinking budgets" explicitly.

**The implication for ML system design**: "Scaling laws" are no longer just about training — a model with 10× inference compute can outperform a model 10× larger at standard inference. This shifts competitive dynamics away from purely raw parameter counts toward efficient "thinking architectures."

### Mechanistic Interpretability — Understanding What's Inside

**Mechanistic interpretability** is the subfield of ML safety and science that attempts to reverse-engineer neural networks to understand *what* they are computing internally — not just what they output.

**The key question**: When a model correctly answers a factual question, where is that fact stored? How is it retrieved? What computation produces the answer? Can we identify and edit specific knowledge?

**Key research findings (2022–2026)**:
- **Superposition hypothesis** (Anthropic): Neural networks can represent far more features than they have neurons, by encoding multiple features in a single neuron via non-interfering overlapping patterns. This makes individual neurons difficult to interpret but explains the model's apparent capacity.
- **Induction heads**: Specific attention head circuits that implement "copy-paste" behavior — crucial for in-context learning. These circuits can be reliably identified and disabled.
- **Knowledge localization**: Certain FFN layers appear to store factual associations (e.g., "Paris is the capital of France"), identifiable and editable via techniques like ROME (Rank-One Model Editing).

**Why it matters**: Mechanistic interpretability is the foundation for:
- Detecting and removing dangerous capabilities (e.g., knowledge of bioweapons synthesis) from specific circuits
- Understanding why models fail on adversarial inputs
- Steering model behavior by directly editing internal representations rather than prompting

### Synthetic Data — The Solution to the Data Wall

Post-2023, a critical limitation has emerged: high-quality human-generated text for training is approaching exhaustion. Internet-scale text has largely been incorporated into the largest models; continued scaling requires new data sources.

**Synthetic data** — data generated by existing ML models — is the primary solution:

**Self-play and iterative refinement**: Generate responses with a powerful model, filter for quality (using another model or rule-based checks), then train on the filtered subset. Repeat. Each round improves the quality frontier. DeepSeek's training heavily used synthetic problem-solution pairs for mathematics and code.

**Distillation as synthetic data**: Train a small model on outputs from a large model — the small model learns from a "synthetic" teacher that generates labeled examples on demand.

**Critical risks**:
- **Model collapse**: Training on synthetic data generated by the same or similar models risks amplifying errors and reducing diversity. Each generation of synthetic data is noisier than human-generated data; models trained on multiple generations of synthetic data may degrade.
- **Distributional narrowing**: Synthetic data tends to be more formulaic than human-generated content — reducing the unexpected connections and diverse styles that make large training corpora valuable.

**The frontier**: Synthetic data works excellently for structured domains (math, code, logic) where outputs can be verified. For open-ended creative or reasoning tasks, quality control remains an unsolved challenge.

## Related
- [[transformer-architecture]]
- [[prompt-engineering]]
