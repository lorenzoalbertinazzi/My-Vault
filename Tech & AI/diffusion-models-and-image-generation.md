---
title: Diffusion Models and AI Image Generation
date: 2026-05-27
tags: [ai, machine-learning, diffusion-models, generative-ai, computer-vision, stable-diffusion, dall-e]
source: research / academic literature
last_updated: 2026-05-27
---

## Summary
Diffusion models are a class of generative AI models that learn to create data (images, audio, video) by learning to reverse a gradual noising process. They have become the dominant paradigm for high-quality image synthesis, powering systems like Stable Diffusion, DALL·E 3, and Midjourney, and have largely supplanted earlier approaches like GANs for photorealistic generation tasks.

## Key Points
- Diffusion models work by learning to **denoise** — they are trained to reverse a process that progressively adds Gaussian noise to data
- The **forward process** adds noise over T timesteps until the data becomes pure noise; the **reverse process** is learned to recover the original data
- A neural network (typically a **U-Net** or transformer) predicts the noise at each step, enabling iterative denoising
- **Latent diffusion models** (LDMs) perform diffusion in a compressed latent space, dramatically reducing compute requirements
- **Conditioning** allows text, images, or other inputs to guide generation (text-to-image, image-to-image, inpainting)
- Key systems: **Stable Diffusion** (open-source, LDM), **DALL·E 3** (OpenAI), **Midjourney**, **Imagen** (Google), **Flux** (Black Forest Labs)

## Details

### From GANs to Diffusion: A Brief History

Generative Adversarial Networks (GANs), introduced by Ian Goodfellow in 2014, dominated image generation for several years. GANs pit a generator network against a discriminator network — the generator tries to fool the discriminator, which tries to distinguish real from fake. This adversarial dynamic produced impressive results (e.g. StyleGAN faces) but suffered from:

- **Mode collapse**: The generator learns only a narrow range of outputs
- **Training instability**: The adversarial objective is fragile and hard to tune
- **Limited diversity**: High quality but low variety

**Variational Autoencoders (VAEs)** offered diversity but lacked sharpness. **Flow-based models** were mathematically elegant but computationally expensive.

Diffusion models, drawing from ideas in non-equilibrium statistical thermodynamics, were formalised for image generation in 2020 (Ho et al., *Denoising Diffusion Probabilistic Models*, NeurIPS 2020) and quickly outperformed GANs on metrics of both quality and diversity.

---

### The Core Mechanics

#### Forward Process (Noise Addition)

Given a clean image **x₀**, the forward process gradually corrupts it by adding small amounts of Gaussian noise over T timesteps (typically T = 1000):

```
x₁, x₂, ..., x_T
```

By timestep T, **x_T** is approximately pure Gaussian noise, indistinguishable from random noise. The noise schedule (β₁, β₂, ..., β_T) controls how much noise is added at each step. A key mathematical property: you can sample **x_t** at any timestep directly from **x₀** without iterating through all steps, using:

```
x_t = √(ᾱ_t) · x₀ + √(1 - ᾱ_t) · ε
```

where ε is Gaussian noise and ᾱ_t is the cumulative noise product.

#### Reverse Process (Denoising / Generation)

The model is trained to predict the reverse: given **x_t**, predict **x_{t-1}** (or equivalently, predict the noise ε that was added). Iterating from pure noise **x_T** through all T reverse steps yields a new clean sample.

The reverse process is parameterised by a neural network ε_θ (x_t, t) trained with the **simplified objective**:

```
L = E[||ε - ε_θ(x_t, t)||²]
```

Simply: mean squared error between actual noise and predicted noise. This simple objective is surprisingly effective and much more stable than GAN training.

---

### The U-Net Architecture

The noise-prediction network is typically a **U-Net** — an encoder-decoder architecture with skip connections that preserve fine-grained spatial information:

- **Encoder**: Downsampling layers that extract features at multiple scales
- **Bottleneck**: Transformer blocks capturing global context (with attention)
- **Decoder**: Upsampling layers that reconstruct spatial detail using skip connections from the encoder

The **timestep t** is injected via sinusoidal positional embeddings (similar to how transformers encode sequence position), allowing the network to adjust its denoising strategy to the noise level.

**Text conditioning** is added via cross-attention layers throughout the U-Net: text embeddings from a language model (e.g. CLIP or T5) are projected and attended over by the image features at each layer.

---

### Latent Diffusion Models (LDMs)

Running diffusion in pixel space is expensive: a 512×512 RGB image has ~786,000 values per sample, and you run T=1000 denoising steps. The key innovation in **Latent Diffusion Models** (Rombach et al., 2022 — the paper behind Stable Diffusion) is performing diffusion in a compressed **latent space**:

1. A **VAE encoder** compresses the image 8×: 512×512 → 64×64 latent
2. Diffusion is performed on the 64×64 latent (8× fewer values)
3. The **VAE decoder** reconstructs the pixel-space image from the denoised latent

This reduces compute by ~64× with minimal quality loss, making training and inference on consumer hardware practical. Stable Diffusion's ~860M parameter model can run on a modern GPU in seconds.

---

### Guidance Techniques

**Classifier-Free Guidance (CFG)**
A critical technique for controlling how closely the output adheres to the text prompt. During training, the model is sometimes run with the text conditioning dropped (replaced with null), teaching it to generate unconditionally. At inference:

```
ε_guided = ε_uncond + w × (ε_cond - ε_uncond)
```

The **guidance scale** w controls the strength: higher w = more faithful to prompt but less diverse; w=1 = no guidance; typical values are 7–12.

**CLIP Guidance**
Alternatively, a CLIP model can steer generation by scoring how well images match a text prompt, using the gradient to update the generation. Less common in modern systems but historically important.

---

### Sampling Algorithms

The original DDPM sampler required all T=1000 steps. Improved samplers dramatically reduce steps needed:

- **DDIM** (Denoising Diffusion Implicit Models): Deterministic sampling, ~50 steps
- **DPM-Solver**: 10–25 steps for similar quality
- **Euler / Heun / DPM++ 2M Karras**: Various ODE solvers used in Stable Diffusion; 20–30 steps typical

Fewer steps = faster generation; trade-off between speed and quality.

---

### Conditioning Modalities

Modern diffusion systems support diverse conditioning:

| Conditioning | Use Case |
|---|---|
| Text (CLIP / T5 embeddings) | Text-to-image generation |
| Image (reference) | Image-to-image translation, style transfer |
| Image + mask | Inpainting (fill masked regions coherently) |
| Depth map | Structure-preserving generation |
| Pose / skeleton | Human pose control (ControlNet) |
| Edge map | Canny-edge guided generation |

**ControlNet** (Zhang et al., 2023) is a training technique that adds auxiliary conditioning inputs to a frozen diffusion model by cloning and fine-tuning the encoder weights with a "zero convolution" bridge, preserving the original model while adding new control.

---

### Key Systems

**Stable Diffusion (Stability AI / Runway)** — Open-source LDM. Multiple versions (1.4, 1.5, 2.0, 2.1, XL, 3). Runs locally, enabling a massive ecosystem of fine-tunes, LoRAs, and extensions (via AUTOMATIC1111, ComfyUI).

**DALL·E 3 (OpenAI)** — Tight integration with ChatGPT. Proprietary. Exceptional prompt adherence, especially for text rendering within images.

**Midjourney** — Commercial, highly aesthetic output. Proprietary architecture. Discord-based interface. Known for painterly, artistic styles.

**Imagen / Imagen 3 (Google DeepMind)** — Uses large T5 language models for text understanding. State-of-the-art photorealism.

**Flux (Black Forest Labs, 2024)** — Open-weight model from original Stable Diffusion authors. Uses a transformer architecture (DiT) rather than U-Net; SOTA quality.

---

### Diffusion Beyond Images

Diffusion has generalised beyond images:
- **Audio**: AudioLDM, MusicGen uses diffusion/flow matching for music generation
- **Video**: Sora (OpenAI), Gen-3 (Runway), Kling use diffusion-based architectures
- **3D**: Point-E, DreamFusion use diffusion for 3D asset generation
- **Protein structures**: RFDiffusion for protein backbone design
- **Molecular design**: Diffusion for drug discovery

---

### Limitations and Challenges

1. **Inference speed**: Still slower than GANs; mitigated by faster samplers and distillation (SDXL-Turbo: 1–4 steps)
2. **Text rendering**: Historically poor at coherent text in images (improving with DALL·E 3 and Flux)
3. **Compositionality**: Struggles to reliably combine multiple distinct objects, attributes, and spatial relationships
4. **Hallucination / faithfulness**: Generating exactly what is described remains challenging for complex prompts
5. **Safety and misuse**: Potential for deepfakes, non-consensual imagery, copyright concerns

## Related
- [[transformer-architecture]]
- [[machine-learning-fundamentals]]
- [[vector-databases-and-embeddings]]
- [[retrieval-augmented-generation]]
