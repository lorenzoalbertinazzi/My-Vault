---
title: Machine Learning Fundamentals
date: 2026-05-26
tags: [tech, AI, machine-learning, neural-networks, deep-learning]
source: research
last_updated: 2026-05-26
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

## Related
- [[transformer-architecture]]
- [[prompt-engineering]]
