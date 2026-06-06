---
title: Federated Learning and Privacy-Preserving Machine Learning
date: 2026-06-06
tags: [tech, AI, federated-learning, privacy, differential-privacy, secure-aggregation, machine-learning, distributed-ml, data-privacy, GDPR, healthcare-AI, edge-computing]
source: "McMahan et al. (2017) 'Communication-Efficient Learning of Deep Networks from Decentralized Data'; Dwork & Roth (2014) 'The Algorithmic Foundations of Differential Privacy'; Bonawitz et al. (2019) 'Towards Federated Learning at Scale'; Kairouz et al. (2021) 'Advances and Open Problems in Federated Learning'"
last_updated: 2026-06-06
---

## Summary
Federated Learning (FL) is a machine learning paradigm where model training is distributed across many clients (devices or organizations) that keep their data locally — the model comes to the data rather than data coming to the model. First introduced by Google in 2016 (McMahan et al. 2017), FL addresses the fundamental tension in AI development: increasingly powerful models require massive datasets, yet the most valuable datasets (medical records, financial transactions, personal communications) cannot be centralized due to privacy regulations, competitive concerns, and ethical constraints. Privacy-Preserving Machine Learning (PPML) is the broader field encompassing FL, differential privacy (DP), secure multi-party computation (MPC), homomorphic encryption (HE), and other techniques that enable learning from sensitive data without exposing it. The stakes are enormous: healthcare AI that could save millions of lives remains constrained because hospitals cannot share patient records; fraud detection models could be vastly improved if banks could learn from each other's data; keyboard prediction models are more accurate when trained on actual user text. FL and PPML provide the mathematical and systems infrastructure to pursue these opportunities without sacrificing privacy.

## Key Points
- Federated Learning: train a global model without centralizing raw data; clients keep data locally, share only model updates (gradients)
- Two main FL paradigms: **cross-device** (millions of mobile/edge devices) vs. **cross-silo** (tens to hundreds of organizations, e.g., hospitals)
- FedAvg (McMahan et al. 2017): the foundational FL algorithm; clients run SGD locally for multiple steps; server averages their weights
- Key challenges: statistical heterogeneity (non-IID data across clients), communication efficiency, system heterogeneity (clients have different compute/bandwidth), privacy of gradients themselves
- **Differential privacy (DP)**: adds calibrated noise to guarantee that an individual's data cannot be inferred from the model or its updates; ε is the privacy budget (smaller = stronger privacy)
- **Secure aggregation**: cryptographic protocol allowing server to compute the sum of client updates without seeing any individual update
- **Gradient inversion attacks** (Zhu et al. 2019) demonstrated that raw gradients can leak training data — FL alone is NOT sufficient for privacy; must combine with DP or secure aggregation
- Production deployments: Google Gboard (keyboard prediction), Apple iOS (QuickType, Siri), healthcare consortia (TriNetX, MELLODDY drug discovery)
- GDPR, HIPAA, and data sovereignty laws create legal demand for FL; the market is projected to reach $196 billion by 2028

## Details

### The Central Paradox: Data-Rich but Privacy-Constrained

Modern AI's performance scales with data. GPT-4 trained on trillions of tokens; AlphaFold trained on 170,000+ protein structures; Google's translation models benefit from billions of translated sentence pairs. Yet the world's most important datasets — medical records of 8 billion people, financial transaction histories, private communications, children's behavior data — are precisely the ones that cannot be aggregated.

**The cost of data silos:**
- **Medical diagnosis**: A pneumonia detector trained on one hospital's 10,000 chest X-rays cannot incorporate the 500,000 X-rays at 100 other hospitals — even though they are all trying to solve the same medical problem
- **Fraud detection**: A bank's fraud model is limited to its own customers' transaction patterns; cross-bank fraud (money laundering networks spanning multiple institutions) is nearly invisible
- **Drug interactions**: Side effects visible only in patients taking specific drug combinations are rare in any single hospital but detectable across a national population

**The regulatory landscape (as of 2026)**:
- **GDPR (EU, 2018)**: Prohibits transfer of personal data outside the EU without specific legal basis; creates strong incentive for in-jurisdiction training
- **HIPAA (US)**: Protected Health Information (PHI) cannot be shared without patient authorization or a specific exception; "de-identification" rules are often insufficient
- **China's Personal Information Protection Law (PIPL, 2021)**: Mirrors GDPR in many respects; explicit restrictions on cross-border data transfer
- **CCPA/CPRA (California)**: Consumer rights over personal data; data minimization principles

Federated Learning provides a technical architecture that can satisfy these regulatory requirements while enabling collaborative model training.

### The FedAvg Algorithm: Foundation of Federated Learning

McMahan, Moore, Ramage, Hampson & Agüera y Arcas (2017) published "Communication-Efficient Learning of Deep Networks from Decentralized Data" (now cited 17,000+ times), introducing the **FedAvg** algorithm:

**Protocol:**

```
Server side:
Initialize global model w^0
For each round t = 1, 2, ..., T:
  Select a fraction C of clients (subset S_t)
  Broadcast current model w^t to S_t
  For each client k ∈ S_t (in parallel):
    Receive w^t
    Run local SGD for E epochs on local dataset D_k:
      For B iterations: w_k ← w_k − η ∇L(w_k; batch)
    Return updated model w_k^{t+1}
  Aggregate: w^{t+1} = Σ_{k ∈ S_t} (n_k/n) · w_k^{t+1}
  where n_k = |D_k| and n = Σ n_k
```

**The key innovation over naive distributed SGD**: Clients run *multiple* local SGD steps (E > 1 epochs) before communication, dramatically reducing communication rounds needed. With E=5 and 100 clients, one communication round does the work of 500 gradient computations but only requires 1 round of communication — critical when clients have slow, intermittent connections.

**Convergence theory**: FedAvg converges to a global optimum (or a neighborhood thereof) under mild conditions; the convergence rate degrades with:
1. Statistical heterogeneity (client data distributions diverge)
2. Large E (more local steps → more "client drift")
3. Partial client participation (only C fraction train per round)

**Communication cost comparison** (McMahan et al. 2017 on CIFAR-10):
- Standard SGD: ~300,000 communication rounds for target accuracy
- FedAvg (E=20, C=0.1): ~1,000 communication rounds — **300× reduction in communication**

### Statistical and System Heterogeneity: The Non-IID Challenge

In standard distributed ML, data is assumed to be IID (identically and independently distributed) across workers. In federated settings, this assumption is violated severely:

**Sources of heterogeneity:**
- **Feature skew**: Users in different countries write texts using different scripts; medical images from different hospital scanners have different contrast/brightness distributions
- **Label skew**: One hospital primarily treats cardiac patients; another primarily treats cancer patients — label distributions are completely different
- **Quantity skew**: Some clients have 100 samples; others have 100,000

**The non-IID problem quantified** (Zhao et al. 2018): On CIFAR-10 with 100 clients, non-IID label distribution (each client has 2 of 10 classes only) degrades FedAvg accuracy from 80% to 55% compared to IID distribution — a 25-point drop.

**Algorithmic solutions:**

1. **FedProx** (Li et al. 2020): Adds a proximal term to local objectives that penalizes deviation from the global model:
```
min_w h_k(w) = F_k(w) + (μ/2)||w - w^t||²
```
The μ parameter controls the tradeoff between local adaptation and global consistency.

2. **SCAFFOLD** (Karimireddy et al. 2020): Uses control variates to correct for client drift — each client maintains a correction term that accounts for the difference between global and local gradients.

3. **Personalized FL (pFL)**: Rather than forcing all clients to converge to a single global model, allow per-client fine-tuning. **Per-FedAvg** (Fallah et al. 2020): Trains a global *initialization* (MAML-style) from which each client can fine-tune with a few gradient steps. Optimal for cases where personalization is explicitly valued (medical imaging: different hospitals have different patient populations).

### Privacy in Federated Learning: The Gradient Leakage Problem

A common misconception: "Federated Learning is private because data stays on devices." **This is false.** Gradients themselves leak information.

**The Deep Leakage attack** (Zhu, Liu & Han 2019, *NeurIPS*): Demonstrated that for a neural network trained on a single sample or small batch, the input image and label can be reconstructed from gradients with near-perfect fidelity in <500 iterations of optimization:

```
Recover input: x* = argmin_{x,y} ||∂L(F(x),y)/∂w − ∂L_actual/∂w||²
```

**R-GAP** (Zhu & Blaschko 2021): Extended gradient inversion to work on deeper networks and batch sizes up to 8–16 images.

**Implication**: Raw gradient sharing, even without sending actual data, provides insufficient privacy guarantees for sensitive applications. True privacy requires cryptographic or statistical protection.

### Differential Privacy: The Gold Standard for Privacy Guarantees

**Differential privacy (DP)**, introduced by Cynthia Dwork (Microsoft Research, 2006), provides a rigorous mathematical definition of privacy:

**Definition**: A randomized mechanism M satisfies **(ε, δ)-differential privacy** if for any two adjacent datasets D and D' (differing in exactly one record) and any output set S:

```
Pr[M(D) ∈ S] ≤ e^ε · Pr[M(D') ∈ S] + δ
```

**Interpretation**: The probability of any outcome changes by at most a factor of e^ε if any single person's data is added or removed. The smaller ε, the stronger the privacy guarantee. δ is a small failure probability (typically 10^{-6} or less).

**The Gaussian mechanism**: To achieve (ε, δ)-DP, add Gaussian noise calibrated to the **sensitivity** of the function (maximum change in output from changing one record):

```
M(D) = f(D) + N(0, σ²)
where σ ≥ Δf · √(2 ln(1.25/δ)) / ε
```

**DP-SGD** (Abadi et al. 2016, Google): The standard differentially private training algorithm:
1. Clip per-sample gradients to bound sensitivity: g ← g/max(1, ||g||₂/C)
2. Add Gaussian noise: g̃ = g + N(0, σ²C²)
3. Average: ḡ = (1/B) Σ g̃
4. Update weights: w ← w − η ḡ

**Privacy accounting (moments accountant)**: Multiple training steps compose — the total privacy cost depends on the number of steps, sample rate, and noise multiplier. The moments accountant provides tight analysis of total ε consumed over T training steps.

**Worked example:**
- Train a classification model for 100 epochs on N=50,000 samples, batch size B=256
- Gradient clipping norm C=1.0
- Noise multiplier σ=1.1
- Total steps T = 100 × (50,000/256) ≈ 19,531
- Per-step sampling probability q = B/N = 256/50,000 ≈ 0.0051
- Using RDP accountant: total privacy cost ≈ ε = 8.0 at δ = 10^{-5}

**ε = 8 interpretation**: For each person's medical record, their inclusion or exclusion changes the probability of any model output by at most e^8 ≈ 2980× — this is weak privacy. ε ≤ 1 is considered strong privacy; ε ≤ 10 is generally considered acceptable for healthcare.

**Accuracy-privacy tradeoff**: DP noise degrades model accuracy. Research benchmarks show:
- MNIST with ε=1: ~1% accuracy drop vs. non-private
- CIFAR-10 with ε=3: ~5–8% accuracy drop
- Large language models: ε=8 privacy adds ~2–4% perplexity increase (research at Google, 2022)

### Secure Aggregation: Cryptographic Approach

Even with DP noise added, the server in FedAvg sees each client's individual update. Secure aggregation (SecAgg) (Bonawitz et al. 2017, Google) allows the server to compute only the *sum* of all client updates without seeing any individual client's update.

**Protocol overview** (simplified):
1. Clients generate pairwise random masks (using Diffie-Hellman key agreement): client i generates mask s_{ij} for each client j
2. Each client sends: u_k = w_k + Σ_{j>k} s_{jk} - Σ_{j<k} s_{kj} (masks cancel in sum)
3. Server computes: Σ_k u_k = Σ_k w_k (masks cancel by construction)
4. Dropout handling: clients who drop out allow their masks to be reconstructed by threshold secret sharing

**Communication overhead**: SecAgg adds O(n²) communication overhead for pairwise key agreement, mitigated in practice by batching and hierarchical aggregation. Google's production SecAgg handles 10 million+ devices per round.

### Homomorphic Encryption and MPC (Advanced Privacy)

**Homomorphic Encryption (HE)**: Allows computation on *encrypted* data without decryption:
```
Enc(x) ⊕ Enc(y) = Enc(x + y)    [additive HE]
Enc(x) ⊗ Enc(y) = Enc(x · y)    [multiplicative HE]
```

Fully Homomorphic Encryption (FHE, Gentry 2009) supports arbitrary computation on encrypted data, but is 10,000–1,000,000× slower than plaintext computation. Practical for inference on encrypted data (e.g., patient sends encrypted medical image; hospital returns encrypted diagnosis), not for full training.

**Secure Multi-Party Computation (MPC)**: Allows multiple parties to jointly compute a function without revealing their inputs. Based on secret sharing: split each value into n shares, distribute across n servers; reconstruct only the aggregate result. SPDZ protocol supports all arithmetic operations. Used for privacy-preserving data analytics across organizations.

### Production Deployments

#### Google Gboard (2017–present)
The flagship FL deployment. Google Gboard (keyboard app) uses FL to improve next-word prediction and emoji suggestions:
- 500M+ Android devices participate
- Training runs on-device during charging + WiFi + idle
- Cross-device FL: massive scale (millions per round), heterogeneous devices (Android phones with different CPUs/memory)
- Model updates are secure-aggregated + differentially private (DP with ε ≈ 2–8 per training run)
- Reported 24% improvement in next-word prediction accuracy over centralized baseline (due to access to actual typing patterns vs. proxy datasets)

#### Apple iOS (2017–present)
Apple uses FL for:
- QuickType keyboard prediction
- Siri voice recognition personalization
- Face ID continuous adaptation
- "Hey Siri" detection threshold tuning

Apple's implementation emphasizes on-device training + secure aggregation without DP (different privacy philosophy than Google — Apple argues that their model aggregation + anonymization provides sufficient protection).

#### MELLODDY Drug Discovery Consortium (2020–2022)
A European Innovative Medicines Initiative (IMI) project involving 10 pharmaceutical companies (Bayer, GSK, Janssen, Novartis, Pfizer, Sanofi, etc.) collaborating to train ML models on combined proprietary drug screening data without sharing the data.

**Architecture**: Federated Multi-Task Learning across 10 silos; each company keeps its proprietary activity data; only model updates shared with a trusted aggregation server. The combined dataset: 20+ billion molecular activity datapoints — an order of magnitude larger than any single company's dataset.

**Results** (published 2022, *Nature Machine Intelligence*): Collaborative FL models outperformed single-company models by up to 20% on external validation benchmarks, particularly for rare disease targets where any single company has limited data.

#### Healthcare Consortia
**TriNetX** and **FL for COVID-19** (2020–2021): A consortium of 20 hospitals in the US, Europe, and Asia trained a COVID-19 prognosis model using FL. The combined dataset (~2,000 ICU patients across sites) produced a model with AUROC = 0.82 — significantly better than any single site's model (best single-site AUROC = 0.71). Published in *Nature Medicine* (Dayan et al. 2021).

**NVIDIA FLARE**: Open-source federated learning framework developed with contributions from Massachusetts General Hospital, NVIDIA, and academic partners; now deployed in >20 hospital networks for collaborative radiology AI.

### Communication Efficiency: Compression Methods

Communication bandwidth is often the bottleneck in FL. Key compression methods:

**Gradient sparsification**: Send only the top-k% of gradient values by magnitude; rest set to zero. TopK with k=0.1% achieves 1000× compression with <2% accuracy loss on standard benchmarks (Aji & Heafield 2017).

**Quantization**: Reduce the bit-width of gradient values from 32-bit float to 8-bit or 4-bit integers. Google's research found 4-bit quantized gradients perform within 0.5% of full-precision on CIFAR-10.

**Structured updates**: Instead of updating the full weight matrix, update only a low-rank decomposition: ΔW ≈ AB^T where A ∈ ℝ^{m×r}, B ∈ ℝ^{n×r} with r << min(m,n). Compresses the update by factor m+n / (m×n) × r.

**Local SGD with reduced communication frequency**: Simple but effective — run 100 local steps between communications instead of 1; communication cost reduced 100×.

### Federated Learning for Large Language Models

The emergence of billion-parameter LLMs creates new FL challenges:
- A 7B parameter model requires ~28GB memory to store parameters alone; impossible on most mobile devices
- Gradient communication for a single step = 28GB of floats

**Solutions (active research area, 2023–2026):**
1. **Split learning**: Split the model across device (bottom layers) and server (top layers); only intermediate activations/gradients communicated. Enables LLM fine-tuning on edge devices without storing full model
2. **Federated fine-tuning with LoRA**: Train only Low-Rank Adapter parameters (<<1% of total parameters) on device; communicate only LoRA updates (megabytes, not gigabytes). Google demonstrated Federated LoRA for Gemma 7B in 2024
3. **One-shot FL**: Each client trains locally and sends a single model; server uses knowledge distillation to merge them without multiple communication rounds

### Expert Debate: Does FL Actually Provide Privacy?

**The skeptical view**: Multiple papers (2020–2023) demonstrate that sophisticated adversaries can:
1. **Gradient inversion**: Reconstruct training samples from gradients (as discussed)
2. **Membership inference**: Determine whether a specific person's data was in the training set (Shokri et al. 2017; attack succeeds with 80%+ accuracy on unprotected FL models)
3. **Model poisoning**: A malicious participant can inject a backdoor by sending crafted model updates; the server averages them in without detection

**The optimistic view**: These attacks require specific conditions (large batch sizes favor defense; DP prevents gradient inversion; Byzantine-robust aggregation prevents poisoning). FL provides genuine privacy benefits over data centralization for realistic threat models.

**The honest consensus** (Kairouz et al. 2021, 58-author survey): FL alone provides "data minimization" — a baseline privacy improvement — but not full privacy protection. Genuine privacy guarantees require FL + DP + SecAgg + robust aggregation in combination. The combination is significantly more complex to deploy and incurs accuracy costs.

### Common Misconceptions

1. **"Federated Learning is private"**: FL alone does not prevent gradient-based privacy attacks. True privacy requires differential privacy and/or secure aggregation on top of the FL protocol.

2. **"DP always makes models much worse"**: At ε = 8–10 (common in practice), accuracy degradation is typically 2–5% on well-designed tasks. At very tight ε ≤ 1, the degradation can be severe (10–30%), but this level of privacy is rarely required.

3. **"FL only works for mobile devices"**: Cross-silo FL (hospitals, banks, pharmaceutical companies) is often more tractable than cross-device FL — fewer clients (10–100), higher reliability, no battery/compute constraints. Most industrial FL deployments are cross-silo.

4. **"FL solves the data governance problem"**: FL reduces legal friction but does not eliminate it. GDPR obligations (right to erasure, data subject access rights) apply even when training is federated. "Machine unlearning" — removing the influence of a specific person's data from a trained model — remains an open research problem.

5. **"Secure aggregation makes FL completely secure"**: SecAgg prevents the server from seeing individual gradients but does not protect against a server that observes multiple rounds (reconstruction attacks across rounds are possible).

## Related
- [[Tech & AI/machine-learning-fundamentals]] — SGD, backpropagation, neural network training; prerequisites for understanding FL
- [[Tech & AI/llm-training-and-scaling-laws]] — Scaling dynamics; challenges of applying FL to billion-parameter models
- [[Tech & AI/agentic-ai-and-multi-agent-systems]] — Multi-agent analogies to multi-party FL; decentralized coordination
- [[Tech & AI/cryptography-fundamentals-and-zero-knowledge-proofs]] — Secure aggregation relies on Diffie-Hellman and secret sharing; ZKPs for verifiable FL
- [[Tech & AI/reinforcement-learning-from-human-feedback]] — RLHF + FL: learning from user feedback without centralizing conversations
- [[Finance/credit-markets-and-credit-risk]] — FL for cross-bank credit risk modeling and fraud detection
- [[Geopolitics/2026-05-30-global-ai-governance-race-to-regulate]] — Data sovereignty laws driving FL adoption
- [[Psychology/memory-systems-and-learning-science]] — Distributed learning as an analogy to distributed memory consolidation
