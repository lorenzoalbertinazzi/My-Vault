---
title: LLM Inference Optimization — Quantization, KV Cache, Speculative Decoding, and Serving
date: 2026-06-06
tags: [tech, AI, LLM, inference, optimization, quantization, speculative-decoding, kv-cache, vllm, flash-attention, serving]
source: research synthesis — Dettmers et al., Leviathan et al., Dao et al., vLLM paper, academic ML literature
last_updated: 2026-06-10
---

## Summary
Training large language models is a one-time, highly parallelizable, GPU-bound operation. Inference — generating tokens for millions of user requests — is an ongoing, memory-bandwidth-bound, latency-sensitive operation that determines the economic viability of deploying LLMs at scale. As LLMs scaled from GPT-2 (1.5B parameters) to GPT-4 (~1.8T in MoE estimates) and Llama 3.1 405B, inference costs became the primary commercial constraint for AI companies. A comprehensive toolkit of optimization techniques has emerged: quantization (reducing numerical precision), KV cache management (avoiding redundant computation), speculative decoding (parallel draft-verify), Flash Attention (IO-aware exact attention), continuous batching (maximizing GPU utilization), and tensor/pipeline parallelism. Together, these techniques have reduced inference cost by 10–100× versus naive implementations, enabling products like ChatGPT to serve millions of concurrent users economically.

---

## Key Points
- LLM inference is **memory-bandwidth bound**, not compute bound — the bottleneck is moving model weights from HBM to GPU compute cores, not arithmetic
- The **KV cache** stores attention keys and values for all previous tokens, avoiding O(n²) recomputation; its memory cost scales as 2 × layers × d_model × sequence_length × batch_size × bytes
- **Quantization** reduces weight precision: FP32 (4 bytes) → FP16 (2 bytes) → INT8 (1 byte) → INT4 (0.5 bytes) → 1-bit (BitNet); each halving reduces memory and bandwidth proportionally
- **Speculative decoding** uses a small draft model to generate k candidate tokens in parallel, then the large target model verifies all k in one forward pass — yielding 2–4× latency speedup
- **Flash Attention** (Dao et al. 2022) reduces memory from O(n²) to O(n) and provides 2–4× wallclock speedup via IO-aware tiling
- **PagedAttention** (vLLM) manages KV cache with virtual memory paging, eliminating fragmentation and enabling 2–4× higher throughput
- **Continuous batching** ("iteration-level scheduling") dynamically adds new requests mid-batch, dramatically improving GPU utilization over static batching
- Real deployment context: GPT-4 API costs declined from ~$0.06/1K output tokens (2023) to ~$0.015 (2024) largely through inference optimization

---

## Details

### The Inference Bottleneck: Why It Differs From Training

**Training:** Massively parallel matrix multiplications (forward + backward pass) over large batches. GPUs operate near theoretical FLOPs utilization because arithmetic intensity (operations per memory byte) is high. NVIDIA A100 at 312 TFLOPS; good training achieves 60–70% MFU (Model FLOPs Utilization).

**Inference (autoregressive decoding):** Token generation is sequential — each token depends on all previous tokens. Even with batch size = 1, the GPU must load *all model weights* from HBM (High Bandwidth Memory) into registers for each single token generated. With a 70B parameter model in FP16, weights = 140 GB, but only ~2 KB of new computation is needed per token step. The arithmetic intensity is extremely low → the operation is bound by HBM bandwidth, not compute.

**Quantitative illustration:**
- H100 GPU: 3.35 TB/s HBM bandwidth, 989 TFLOPS (BF16)
- Roofline model: arithmetic intensity breakeven = 989T FLOPs / 3.35 TB/s ≈ 295 FLOPs/byte
- Autoregressive token generation arithmetic intensity ≈ 1–5 FLOPs/byte
- → Operating at <2% of theoretical compute; bandwidth-bound by a factor of ~200×

This bandwidth-bound nature explains why quantization is so effective: INT4 weights take 4× less bandwidth than FP16, approximately 4× faster to load → approximately 4× more tokens per second for memory-bound workloads.

**Prefill vs. Decode Phases:**
LLM inference has two distinct phases:
1. **Prefill:** Process the entire input prompt in one large, parallel matrix multiplication. Compute-bound for long prompts; can be batched across multiple users' prompts efficiently.
2. **Decode:** Autoregressive token generation, one step at a time. Memory-bandwidth-bound; latency is sequential.

Systems must optimize for both: prefill latency (time to first token, TTFT) and decode throughput (tokens per second).

### KV Cache: Mechanism and Memory Math

**The Problem Without KV Cache:**
In the attention mechanism, each new token must attend to all previous tokens. Without caching:
- For token position n, compute attention over all n tokens: O(n) work
- For a sequence of N tokens total: O(N²) total attention work
- For a 4096-token context: 4096² ≈ 16.7M attention computations per layer

**The KV Cache Solution:**
Store the Key (K) and Value (V) projections for every previously processed token in GPU memory. For token n+1, only compute K,V for the new token; retrieve cached K,V for all previous tokens.

This reduces decode computation from O(N²) → O(N) per new token (linear in context length, not quadratic).

**KV Cache Memory Formula:**
```
Memory = 2 × num_layers × num_heads × d_head × sequence_length × batch_size × bytes_per_element
```

For Llama 3.1 70B (FP16): 80 layers, 8 KV heads (GQA), d_head = 128, seq_len = 4096, batch = 1:
```
= 2 × 80 × 8 × 128 × 4096 × 1 × 2 bytes
= 2 × 80 × 8 × 128 × 4096 × 2
≈ 2.68 GB per request
```

For batch size 32 with 4096 tokens: ~85.9 GB — more than one A100's entire HBM. This explains why KV cache memory is the primary constraint on batch size at long contexts.

**Grouped Query Attention (GQA):**
Llama 3 and many modern models use GQA — sharing K,V projections across groups of query heads (e.g., 8 query heads share 1 KV head). This reduces KV cache memory by 8× with minimal quality impact. Meta's Llama 3.1 70B uses GQA with 8 KV heads vs 64 query heads — 8× KV cache reduction.

**Multi-Query Attention (MQA):**
Extreme form: all query heads share a single K,V projection. Used in some models (PaLM) for maximum KV cache reduction; more quality degradation than GQA.

### Quantization: From FP32 to 1-Bit

**Why Quantize:**
1. Memory reduction: INT4 fits 8 weights per byte vs. FP16's 0.5 weights per byte — 8× smaller
2. Bandwidth: proportional reduction in HBM reads → proportional latency improvement (bandwidth-bound)
3. Compute: INT8 arithmetic is 2–4× faster than FP16 on NVIDIA GPUs (due to tensor core design); INT4 further gains

**Quantization Schemes:**

**FP16/BF16 (16-bit):** The standard inference precision for most deployed models. BF16 has the same 8-bit exponent as FP32 (wider dynamic range than FP16) with reduced mantissa. Most training uses BF16/FP16.

**INT8 (LLM.int8, GPTQ):**
Tim Dettmers et al. (2022) showed that naively quantizing activations to INT8 causes serious quality degradation because ~0.1% of outlier activations have ~100× larger magnitude than typical activations. LLM.int8 handles this via **mixed-precision decomposition**: outlier channels remain in FP16, others in INT8. Result: near-lossless quality at INT8 with ~1.7× memory reduction and moderate speedup.

**INT4 (GPTQ, AWQ, GGUF):**
GPTQ (Frantar et al. 2022) applies optimal brain quantization (OBQ) to compress weights to 4-bit with minimal perplexity increase. AWQ (Lin et al. 2023) improves by searching for per-channel scaling factors that minimize quantization error for the most important (highest-activation) weights.

**Empirical quality degradation (LLaMA-2 70B):**
- FP16 baseline: perplexity = 3.31
- INT8: perplexity = 3.32 (negligible)
- INT4 (AWQ): perplexity = 3.37 (+1.8%)
- INT4 (GPTQ): perplexity = 3.38 (+2.1%)
- INT3: perplexity > 3.60 (notable degradation)

**1-Bit LLMs (BitNet):**
Microsoft Research's BitNet b1.58 (Wang et al. 2023, Ma et al. 2024) quantizes weights to {-1, 0, +1} — the ternary representation. The paper claims 1-bit LLMs at sufficient scale match FP16 quality while using dramatically less memory and compute (no multiplications needed in the attention computation — only additions).

**Skeptical assessment:** The results at small scale (1B–3B) are promising; at very large scale (100B+), quality gaps appear in complex reasoning tasks. BitNet 2025 improvements continue to close this gap. Still early-stage for production.

**Quantization-Aware Training (QAT) vs. Post-Training Quantization (PTQ):**
- PTQ (GPTQ, AWQ): apply quantization *after* training on a calibration dataset; fast but some quality loss
- QAT: fine-tune with simulated quantization noise during training; better quality but requires access to training pipeline

### Speculative Decoding

**The Bottleneck:**
Autoregressive generation requires N forward passes for N tokens — sequential and slow. Speculative decoding breaks this constraint by generating multiple candidate tokens in parallel.

**How It Works (Leviathan, Kalman & Matias, 2022 / Chen et al., 2023):**
1. A small, fast **draft model** (or the target model's early layers) generates k candidate tokens autoregressively (fast, because the draft model is small)
2. The large **target model** processes all k candidates *in a single parallel forward pass* (the equivalent of processing a batch of one k-length continuation)
3. Compare target model's probability distribution with draft's token choices:
   - If the target model would have chosen the same token: **accept**
   - If not: **reject** with probability proportional to the probability ratio, resample from target's distribution
4. On average, accept β tokens (where β depends on the agreement between draft and target); guaranteed to produce the identical distribution as the target model alone (no quality degradation)

**Speedup math:**
If β ≈ 0.8 (80% acceptance rate) and k = 5:
Expected tokens generated per target forward pass ≈ k · β/(1-β) + 1 ≈ 5 · 0.8/(0.2) + 1 ≈ 5

The target model processes 5 tokens in one pass (like a prefill) instead of 1 → ~5× effective generation rate.

Practical speedups: 2–4× for typical use cases (benchmark on code generation, math) because acceptance rate varies by task and domain alignment between draft and target.

**Draft model choices:**
- Dedicated small model: Google DeepMind's Medusa (multiple draft heads on same model), or external small LLaMA (7B) as draft for 70B target
- Layer-skipping: use the first N/2 layers as draft, full model as verifier
- n-gram look-up ("retrieval-based speculation"): for repetitive text, next tokens can be retrieved from context itself

**Limitations:** Speculative decoding does not help throughput in high-batch settings (the target model is already compute-bound), only latency in low-batch settings. Requires carefully matched draft and target model vocabulary/tokenizer.

### Flash Attention: IO-Aware Exact Attention

**The Problem:**
Standard attention computes the attention score matrix: A = softmax(QKᵀ/√d) · V
For sequence length N: Q, K, V each of shape N × d; score matrix N × N.
This requires materializing the N × N attention matrix in HBM: memory = O(N²) × bytes.

For N = 8192 (GPT-4 context), d = 128, FP16: attention matrix ≈ 8192² × 2 bytes ≈ 134 MB *per layer*. With 96 layers → ~13 GB just for attention matrices, plus repeated reads/writes = massive HBM I/O bottleneck.

**Flash Attention Solution (Dao, Fu, Ermon, Rudra, Ré, 2022):**
Compute attention in tiles that fit in GPU SRAM (L2 cache, ~40 MB for A100), fusing all attention operations into a single kernel, never materializing the full N×N matrix in HBM.

**The key insight:** Numerically stable online softmax (Milakov & Gimelshein, 2018) allows computing softmax incrementally over blocks. Flash Attention computes the block attention outputs and the running softmax normalization in SRAM, combining them without ever reading/writing the full N×N matrix.

**Flash Attention 2 (2023)** added improved parallelism across sequence length, better work partitioning, and reduced non-matmul FLOPs.
**Flash Attention 3 (2024)** targeted H100's asynchronous memory engine and TMA (Tensor Memory Accelerator), achieving >750 TFLOPS on H100.

**Speedup:** 2–4× wallclock time for attention on typical sequence lengths; 5–9× on very long sequences (>16K tokens). Memory: O(N) instead of O(N²) for intermediate activations → enables much longer contexts without OOM.

### PagedAttention and vLLM

**The KV Cache Memory Fragmentation Problem:**
In serving settings, requests have variable sequence lengths and unknown output lengths. If you pre-allocate a maximum context window (e.g., 4096 tokens) for each request, most of that space is wasted (short outputs). If you allocate only what's needed, you get memory fragmentation — gaps too small to use.

**PagedAttention (Kwon, Li, Zhuang et al., 2023; vLLM):**
Inspired by virtual memory paging in operating systems:
- KV cache is divided into **fixed-size blocks** (pages) of k tokens each
- Each request maintains a **block table** mapping logical positions to physical memory pages
- Pages are allocated on demand as tokens are generated
- **Memory waste < 4%** (only the last page per sequence can be partially empty)

Additional benefit: **Copy-on-write semantics** enable multiple requests that share a common prefix (system prompt) to share their KV cache pages until their outputs diverge — especially powerful for multi-turn conversations and RAG (where system prompts + documents are repeated across users).

**vLLM throughput gains:** 2–4× vs. Hugging Face Transformers (static batching) and 1.3–2.1× vs. ORCA (continuous batching without paged KV cache).

**Alternative: MooncakeStore, Infinite-LLM (2024):** Disaggregated KV cache storage — KV caches are stored on CPU or remote DRAM, offloaded from GPU when the GPU needs space, reloaded for subsequent turns. Enables very long context serving at the cost of PCIe bandwidth.

### Continuous Batching ("Iteration-Level Scheduling")

**The Static Batching Problem:**
Naive serving systems create a batch of N requests, process them together until all N are complete (the longest one), then accept a new batch. Problem: shorter requests finish early, and their GPU compute sits idle waiting for the long-tail requests.

**Continuous Batching (ORCA, Yu et al., 2022):**
At every decode iteration (every new token), check for newly completed sequences and replace them with waiting requests. GPU is never idle waiting for a slow sequence.

Result: ~36× higher throughput vs. static batching in the ORCA paper's experiments on GPT-2 175B workloads.

This is now the standard in all production LLM serving systems (vLLM, TGI, TensorRT-LLM, SGLang).

### Tensor and Pipeline Parallelism

For models too large to fit on a single GPU, distributing across multiple GPUs is required:

**Tensor Parallelism (Megatron-LM, Shoeybi et al.):**
Split individual weight matrices across GPUs — each GPU holds a shard of each layer. Requires AllReduce communication between layers.
- Pros: every GPU participates in every token; low latency
- Cons: requires fast interconnect (NVLink or InfiniBand); communication overhead scales with model parallelism degree

**Pipeline Parallelism:**
Split the model into stages; each GPU holds consecutive layers. Input passes through stage 1, then stage 2, etc. (like an assembly line).
- Pros: less inter-GPU communication (only activations between adjacent stages)
- Cons: GPU pipeline bubbles (idle stages waiting for previous stage); higher latency for single requests

**Expert Parallelism (for MoE models):**
Each GPU holds a subset of experts; routing algorithm directs tokens to the relevant GPU. Very efficient for MoE models (GPT-4, Mixtral, Grok).

**Practical configuration (Llama 3.1 405B):**
Meta's deployment: 8 A100-80GB GPUs minimum (8-way tensor parallelism, 405B × 2 bytes FP16 / 8 GPUs ≈ 101 GB/GPU — just fitting). With INT4 quantization: 405B × 0.5 bytes / 8 GPUs ≈ 25 GB/GPU — leaves abundant space for KV cache.

### Throughput vs. Latency Tradeoffs

**Key metrics:**
- **Time to First Token (TTFT):** How long until the first output token arrives; driven by prefill latency
- **Time Per Output Token (TPOT) / tokens/sec:** Decode speed; driven by bandwidth
- **Tokens/sec/GPU:** Throughput efficiency; primary cost metric

**Batching tradeoff:**
Larger batches → higher GPU utilization → higher throughput but higher TTFT and TPOT (each user waits longer).
Smaller batches → lower throughput but lower latency.

Production systems set SLOs (Service Level Objectives): e.g., "p95 TTFT < 2 seconds, p95 TPOT < 100ms/token" and maximize batch size within those constraints.

**The memory-compute tradeoff in quantization:**
INT4 reduces memory by 4× vs. FP16; in memory-bandwidth-bound decode, this approximately 4× increases tokens/second. However, quantized matmul may require dequantization overhead → real speedup ~2–3× for INT4 vs. FP16 in practice.

### Real-World Economics

**GPT-4 cost trajectory:**
- March 2023 launch: $0.06/1K output tokens (inference cost ~$0.02, margin ~67%)
- November 2023 (GPT-4 Turbo): $0.03/1K output tokens — 50% reduction
- May 2024 (GPT-4o): $0.015/1K output tokens — 75% from launch price
- Cost reductions driven by: distillation/speculative decoding, quantization, custom silicon (Trainium2-equivalent), operational efficiency

**Self-hosting economics (2024):**
Llama 3.1 8B, INT4, H100 GPU ($30K), continuous batching:
- Peak throughput: ~8,000 tokens/second
- Cost/hour: ~$2 (cloud H100 spot pricing)
- Cost/1M tokens: $2 / (8000 × 3600) × 1e6 ≈ **$0.069/1M tokens**
- Llama 3.1 405B, INT4, 8×A100: peak ~800 tokens/second
- Cost/1M tokens ≈ **$3.50/1M tokens** (comparable to API pricing)

**Environmental note:**
GPT-4's estimated training cost: ~$100M of compute, ~500–1,000 MWh of energy.
Inference at scale: a model serving 100M users daily at 1K tokens/query consumes ~50–100 MWh/day (rough estimate depending on model size). Inference energy is now comparable to or greater than training energy across a model's deployment lifetime.

---

### 2026 Inference Stack: FP8 Quantization, SGLang, and the Combined Optimization Ceiling

**FP8 Quantization: The 2026 Standard:**
NVIDIA's H100 GPU introduced native FP8 (8-bit floating point, two formats: E4M3 and E5M2) tensor core acceleration in 2023, but production deployment at scale required 12–18 months of framework maturation. By 2026, FP8 training and inference has become the default precision for frontier model inference on H100/H200 hardware:

**FP8 format comparison:**
- **E4M3:** 4 exponent bits, 3 mantissa bits; range [-448, 448]; preferred for activations (moderate range, higher precision)
- **E5M2:** 5 exponent bits, 2 mantissa bits; range [-57344, 57344]; preferred for gradient storage (wider range, lower precision)

**Quality vs. speedup:** FP8 inference achieves approximately 1.5–1.8× throughput improvement over BF16 on H100 with <0.5% quality degradation on standard benchmarks (MMLU, HumanEval) for models ≥7B parameters. The throughput gain is primarily from reduced memory bandwidth consumption (FP8 weights and activations = 1 byte vs. 2 bytes for BF16) and faster tensor core execution (H100 delivers 3.9 PFLOPS FP8 vs. 2.0 PFLOPS BF16).

**Combined optimization stack performance (empirical, H100 SXM5):**
The production-proven combined optimization stack — FP8 quantization + Flash Attention 3 + continuous batching + speculative decoding with a 7B draft model — achieves **5–8× better cost-efficiency** than naive FP16 inference with static batching on a 70B-parameter model:

| Optimization | Individual Contribution |
|---|---|
| FP8 vs. FP16 | 1.5–1.8× throughput |
| Flash Attention 3 vs. standard | 1.3–1.5× (long context) |
| Continuous vs. static batching | 2–4× throughput |
| Speculative decoding (7B draft → 70B) | 2–3.9× latency reduction |
| Combined effect (multiplicative) | 5–8× overall cost efficiency |

A concrete measured result (Rodrigo Ekstein, 2025, reproduced in Zylos Research 2026): speculative decoding reduced a 70B model's inference time from **136.14 seconds to 35.17 seconds** for a standardized long-form generation task — a **3.87× speedup** with identical output quality.

**vLLM and SGLang: The 2026 Production Infrastructure Duopoly:**
Two frameworks dominate production LLM serving in 2026:

**vLLM (Berkeley, 2023–):** The industry reference implementation for production LLM serving. As of v0.6 (2026), vLLM supports: PagedAttention + prefix sharing, FP8/INT4/INT8 quantization via GPTQ/AWQ/GGUF, speculative decoding with multiple draft strategies (Eagle, Medusa, n-gram), disaggregated prefill/decode, and native MoE expert parallelism. Used in production by Azure OpenAI, Amazon Bedrock, and hundreds of self-hosted deployments.

**SGLang (Stanford, 2024–):** Designed specifically for **compound AI systems** — pipelines where multiple LLM calls are structured and depend on each other. SGLang's key innovation is **RadixAttention**, which extends PagedAttention's prefix caching to tree-structured KV cache sharing across a radix tree of request prefixes:
```
Request 1: [System prompt A] + [User Q1]  → shares System prompt A KV
Request 2: [System prompt A] + [User Q2]  → with request 1
Request 3: [System prompt B] + [User Q3]  → independent
```
RadixAttention enables **2–5× higher throughput for structured generation workloads** (multi-turn conversations, RAG, multi-agent pipelines) versus PagedAttention because the shared system prompt and document context KV cache is stored once and reused across all requests using that context. By 2026, SGLang has become the preferred framework for agentic AI serving deployments.

**Staircase Streaming for Multi-Agent Inference (arXiv 2510.05059):**
An emerging serving optimization for multi-agent pipelines where multiple LLM calls execute in a pipeline: rather than waiting for each agent's full response before passing to the next, staircase streaming enables the downstream agent to begin processing the upstream agent's partial output token-by-token. For pipelines where agent A's early tokens determine agent B's task setup, this reduces end-to-end latency by 30–50% without changing output quality. Currently implemented in research versions of LangGraph and will reach production frameworks in 2026.

### The Inference Economics Trajectory (2023–2026)

A structured view of how inference costs have declined for GPT-4-class intelligence:

| Date | Provider/Model | Cost/1M Output Tokens | Key Optimization |
|------|---------------|----------------------|-----------------|
| Mar 2023 | OpenAI GPT-4 | $60.00 | Baseline |
| Nov 2023 | OpenAI GPT-4 Turbo | $30.00 | Hardware + batching |
| May 2024 | OpenAI GPT-4o | $15.00 | Distillation + quant |
| Jan 2025 | DeepSeek V3 | $0.28 | MoE + FP8 training |
| Jun 2026 | DeepSeek V3.2 | $0.04 | Full stack optimization |

This 1500× cost reduction over 39 months is the most rapid decline in the cost of a specific cognitive capability in recorded technological history. The economic implication: AI inference is approaching the commodity range where embedding intelligence into every application interaction is economically trivial.

## Related
- [[transformer-architecture]]
- [[llm-training-and-scaling-laws]]
- [[mixture-of-experts-architecture]]
- [[prompt-engineering]]
- [[retrieval-augmented-generation]]
- [[agentic-ai-and-multi-agent-systems]]


### Saturday Cross-Disciplinary Synthesis: Inference Optimization as Economics of Intelligence

**Connection to Economics — The Marginal Cost of Intelligence:**
LLM inference optimization is fundamentally an economics problem: how to minimize the marginal cost of a unit of AI intelligence (cost per output token) to expand the market for AI applications. Moore's Law provided three decades of cost reduction in compute through hardware improvement; inference optimization provides a second lever — algorithmic efficiency — that in some dimensions has outpaced hardware progress. The marginal cost of GPT-3-class intelligence has fallen approximately 100× from 2020 to 2026 through a combination of hardware (H100 vs. A100 vs. V100 performance improvement), quantization (reducing from 32-bit to 4-bit precision at < 5% quality loss), and architectural efficiency (Mixture of Experts, speculative decoding). At the current cost trajectory, inference at GPT-3 quality will cost approximately $0.001/1000 tokens by 2028 — approaching the cost range where every internet interaction can include AI intelligence as a default feature.

**Connection to Physics — Thermodynamic Limits of Computation:**
The Landauer principle (1961) establishes the theoretical minimum energy required to erase one bit of information: kT ln2 (approximately 10⁻²¹ joules at room temperature). Current AI inference hardware operates approximately 10 million times less efficiently than this theoretical limit — meaning there is extraordinary room for energy efficiency improvement before hitting physical limits. Neuromorphic computing architectures (see [[neuromorphic-computing]]) that use spike-based computation (only firing when neurons are active, unlike always-on GPU computation) approach biological neural network energy efficiency (~20 watts for the human brain performing inference equivalent to billion-parameter models). The energy economics of AI inference is becoming a macroeconomic and geopolitical variable: global AI inference energy consumption is projected to reach 100+ TWh/year by 2030, comparable to the electricity consumption of mid-sized countries, creating energy supply chain and carbon footprint concerns that influence both business strategy and public policy.

**Connection to Cognitive Science — Computational Complexity and Human Reasoning:**
The parallel between LLM inference and human cognition extends to computational complexity: both systems face resource constraints (GPU memory and compute vs. working memory and neural energy budget) that require approximation strategies rather than exact computation. Human "satisficing" (choosing a "good enough" option rather than optimizing exhaustively) and LLM "speculative decoding" (using a small model to speculatively generate tokens, verified by the large model) are both approximation strategies that trade optimality for speed under resource constraints. The cognitive science implication: understanding how LLMs optimize inference — which approximations they make, what information they prune — may illuminate analogous processes in human cognition, as both systems are implementing approximate Bayesian inference under computational constraints.

**Updated Related Connections:**
- [[Finance/macroeconomics-101]] — Energy economics of AI inference; AI compute as strategic resource with macro implications
- [[Tech & AI/neuromorphic-computing]] — Spike-based computation approaching thermodynamic efficiency limits
- [[Geopolitics/2026-05-27-us-china-great-power-competition]] — AI chip export controls and inference optimization as strategic competitive dimensions
