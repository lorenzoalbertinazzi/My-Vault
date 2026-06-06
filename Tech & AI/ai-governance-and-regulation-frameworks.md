---
title: AI Governance and Regulation Frameworks
date: 2026-06-06
tags: [tech, AI, governance, regulation, EU-AI-Act, NIST, risk-management, algorithmic-accountability, transparency, bias, fairness, safety, international-standards, GDPR, responsible-AI, AI-policy, digital-rights, frontier-AI, compute-governance]
source: "EU AI Act (Regulation 2024/1689); NIST AI Risk Management Framework 1.0 (2023); Blueprint for an AI Bill of Rights (White House OSTP, 2022); OECD AI Principles (2019); UN Secretary-General Report on AI Governance (2023); Bletchley Declaration (2023); Seoul AI Summit Joint Statement (2024); Paris AI Action Summit Communiqué (2025); Anthropic Model Card (Claude 3); DeepMind Responsible Development Policy (2024)"
last_updated: 2026-06-06
---

## Summary
AI governance encompasses the laws, regulations, standards, voluntary commitments, and international agreements that govern the development, deployment, and use of AI systems. The period 2022–2026 has produced the most significant regulatory activity in AI history: the EU AI Act became the world's first comprehensive AI law; the US published national AI strategy documents and executive orders; the G7, G20, and UN have all issued AI governance frameworks; and bilateral AI safety agreements between the US, UK, EU, China, and Japan represent the first tentative steps toward international AI arms control. Understanding this landscape requires integrating technology policy, law, ethics, and geopolitics.

## Key Points
- The EU AI Act (2024) is the world's most comprehensive AI law: risk-based classification (unacceptable/high/limited/minimal risk) with prohibitions on social scoring, real-time biometric surveillance, and subliminal manipulation
- NIST AI RMF provides voluntary US framework: Govern, Map, Measure, Manage — aligned with risk management rather than prohibition
- The US-China AI safety agreement (Bletchley 2023 + Seoul 2024) is symbolically significant but substantively thin: agreement on catastrophic risk communication protocols, no restrictions on capability development
- "Frontier AI" governance — regulating the most powerful models — is the contested frontier: compute thresholds, incident reporting, independent safety auditing
- The compliance gap between regulation and enforcement is wide: EU AI Act high-risk requirements (conformity assessment, transparency, human oversight) are largely not yet enforced; most companies are in "prepare-to-comply" rather than "in compliance" mode

## Details

### The Global AI Governance Landscape

**EU AI Act (Regulation 2024/1689) — The Global Standard-Setter:**
The EU AI Act, politically agreed in December 2023 and entering into force August 2024, is the world's first comprehensive AI regulation. It applies to any AI system placed on the EU market or used in the EU, regardless of where the developer is located — creating a de facto global standard (the "Brussels Effect") for any company that wants EU market access.

**Risk classification:**
- **Unacceptable risk (prohibited):** Social scoring by governments, real-time remote biometric identification in public spaces (with law enforcement exceptions), manipulation of behavior through subliminal techniques, exploitation of vulnerabilities of specific groups. Penalty: up to €35M or 7% of global annual turnover.
- **High risk:** Systems used in critical infrastructure, education, employment, essential services, law enforcement, migration, justice. Requirements: conformity assessment, data governance, transparency to users, human oversight, accuracy monitoring, post-market surveillance. Penalty: up to €15M or 3% of turnover.
- **GPAI (General Purpose AI) — New Category for Foundation Models:** Models trained on broad data at high computational effort (≥10²³ FLOPs defined as "systemic risk" threshold) must: publish technical documentation, implement copyright law compliance, report serious incidents to EU AI Office. Systemic-risk models additionally require adversarial testing, cybersecurity measures, and energy efficiency reporting.
- **Limited/minimal risk:** Transparency requirements (chatbots must identify as AI; deepfakes must be labeled). Most AI applications fall here.

**Key dates:**
- February 2025: Prohibited practices prohibition took effect
- August 2025: GPAI obligations and AI Office operationalized
- August 2026: High-risk AI system requirements take effect
- August 2027: Certain high-risk systems (safety components, products) compliance deadline

**NIST AI Risk Management Framework (AI RMF 1.0, January 2023):**
The US approach to AI governance relies on voluntary standards rather than mandatory regulation. The AI RMF organizes AI risk management into four functions:
- **Govern:** Establishing organizational policies, culture, and accountability structures for responsible AI
- **Map:** Identifying and characterizing AI risk context, impact, and affected stakeholders
- **Measure:** Analyzing and assessing AI risks using qualitative and quantitative methods
- **Manage:** Prioritizing and treating risks through controls, monitoring, and governance processes

The AI RMF has been adopted by hundreds of organizations as a voluntary framework and is cited in US federal procurement requirements, creating de facto influence on companies that sell AI to the federal government. The National AI Advisory Committee (NAIAC) is developing sector-specific AI RMF profiles for healthcare, finance, critical infrastructure, and defense.

### International Frameworks and Diplomatic Agreements

**The Bletchley Declaration (November 2023):**
The UK AI Safety Summit at Bletchley Park produced the first international AI safety agreement. Signed by 28 countries including the US, China, UK, EU, India, and Japan, the declaration: (1) acknowledged that frontier AI may pose existential and catastrophic risks; (2) committed signatories to collaborating on AI safety research; (3) established the process for ongoing international AI safety dialogue. The China signature was diplomatically significant — the first time China endorsed multilateral acknowledgment of AI catastrophic risk. The substantive content was thin: no binding commitments, no restrictions on capability development, no verification mechanisms.

**Seoul AI Summit (May 2024):**
Advanced the Bletchley process with: voluntary "AI Seoul Accord" where frontier AI companies (OpenAI, Google DeepMind, Anthropic, Meta, xAI, Samsung, Naver) committed to safety frameworks, incident reporting, and red-teaming before model releases; establishment of AI Safety Institutes in 10+ countries; and agreement on an international network of AI safety institutes to share research and testing methodologies.

**Paris AI Action Summit (February 2025):**
The French-hosted summit focused on "AI for the public good" rather than frontier safety. Key outcomes: AI Action Coalition with 50+ countries committing to AI deployment for climate, health, and education; revised OECD AI Principles incorporating frontier AI provisions; and the "Global Partnership on AI" (GPAI) merger into the OECD AI Policy Observatory.

### Compute Governance: The New Frontier

The most technically sophisticated AI governance development is compute governance — regulating access to the computational resources required to train frontier AI models, rather than regulating the models themselves. The logic: if training a dangerous AI requires more than a defined threshold of compute (currently ~10²⁶ FLOPs for GPT-4 class), then restricting access to that compute limits who can train frontier models.

**US BIS Chip Export Controls (October 2022, updated 2023, 2024):**
Restricts export of advanced AI chips (NVIDIA A100, H100 GPUs; advanced FPGA configurations) to China, Russia, and ~40 other countries without license. The 2023 update closed loopholes (restricting equivalent-performance chips regardless of product name); the 2024 update added "controlled countries" for chip exports to prevent Singapore, UAE, and Malaysia re-export to China. Economic effect: Chinese AI labs have been limited to less powerful domestic chips (Huawei Ascend 910B, ~50–70% of H100 performance) or expensive grey-market H100 smuggling routes. The policy logic: a 5–10 year compute gap creates a corresponding capability gap in the most compute-intensive models.

**EU AI Act Compute Threshold:**
The 10²³ FLOPs GPAI training compute threshold (below which the "systemic risk" provisions don't apply) is compute governance embedded in regulation — creating an incentive to train models just below the threshold and a clear line above which additional obligations apply.

**Challenges:**
- **Model efficiency gains undermine compute thresholds:** If algorithmic progress makes the same capability achievable with less compute (as with Llama-3 achieving GPT-4 quality at 1/10th the compute), fixed compute thresholds become ineffective
- **Algorithm proliferation:** Open-source models spread training techniques, making exclusive control of powerful AI impossible
- **China's semiconductor program:** SMIC's 7nm process (2023) and domestic GPU development program reduce the long-term efficacy of chip export controls

### Corporate Governance: Responsible AI Frameworks

**Anthropic's Constitutional AI and Model Cards:**
Anthropic developed Constitutional AI (2022) as a technical approach to alignment that reduces dependence on human labelers: the AI is trained to critique and revise its own outputs based on a "constitution" of principles (derived from UDHR, Apple's terms of service, and other normative sources). Model cards (technical documentation of model capabilities, limitations, and appropriate use cases) are published with each Claude version. The "responsible scaling policy" commits Anthropic to not deploying models above capability threshold X without safety measures Y — a forward commitment on the safety-capability tradeoff.

**Google DeepMind's Responsible AI Practice:**
DeepMind's framework emphasizes beneficial use, privacy by design, fairness, accountability, and safety. The "safety level" framework (SL-1 through SL-5, with SL-4 and SL-5 representing potentially catastrophic risks) parallels biosafety level (BSL) classifications in virology — treating AI safety as an empirical safety science requiring graduated containment protocols.

**Microsoft's Responsible AI Standard:**
Microsoft's framework (2022, updated 2023) covers six principles: fairness, reliability and safety, privacy and security, inclusiveness, transparency, and accountability. Each principle has operationalized "goals" with accompanying evaluation metrics — moving from philosophical commitment to engineering specification. The organizational implementation: Responsible AI teams embedded in product groups, a RAISE (Responsible AI Strikes and Experience) committee for escalations, and mandatory Responsible AI Impact Assessment (RAIA) for high-risk features.

### The Regulatory Gap: Enforcement and Compliance

The most significant observation about AI governance in 2026 is the gap between regulatory text and actual compliance. The EU AI Act's high-risk requirements — conformity assessments, data governance documentation, human oversight mechanisms, post-market monitoring — require organizational capabilities that most affected companies have not yet built. The EU AI Office (established September 2024) has a staff of ~100 officials with responsibility for overseeing GPAI models across the entire EU market — a staffing ratio that makes intensive oversight of hundreds of foundation models mathematically impossible.

The US enforcement landscape is similarly early: FTC AI enforcement actions (advertising deception, unfair dark patterns) address symptoms rather than systemic risks; the proposed TAKE IT DOWN Act (deepfakes) addresses specific harms; sector-specific AI rules (FDA for medical AI, CFPB for financial AI) are developing independently without a unifying framework.

The practical implication: compliance-focused companies are investing in documentation, impact assessments, and governance processes that position them for future enforcement; compliance-averse companies are accumulating regulatory risk that materializes when enforcement catches up with deployment — which the EU AI Act's phase-in schedule suggests will begin in 2026–2027.

## Cross-Disciplinary Connections

**To Finance:** AI Act compliance requirements (transparency, human oversight, documentation) impose compliance costs on financial services AI — credit scoring, AML, insurance pricing, algorithmic trading. See [[Finance/quantitative-finance-and-algorithmic-trading]].

**To Psychology:** Behavioral design constraints (prohibitions on subliminal manipulation, exploitation of vulnerabilities) directly implement behavioral psychology insights about how digital interfaces can exploit cognitive biases. See [[Psychology/cognitive-biases]].

**To Geopolitics:** AI governance is inseparable from US-China strategic competition — every governance framework embeds assumptions about democratic vs. authoritarian AI deployment that reflect underlying geopolitical values. See [[Geopolitics/2026-05-27-us-china-great-power-competition]].

## Related
- [[ai-safety-and-alignment]] — Technical AI safety research underlying governance frameworks
- [[agentic-ai-and-multi-agent-systems]] — Agentic AI governance challenges; autonomous systems regulation
- [[llm-training-and-scaling-laws]] — Compute thresholds and frontier AI regulation
- [[federated-learning-and-privacy-preserving-ml]] — Privacy-preserving AI as governance-compliant technical design
- [[cryptography-fundamentals-and-zero-knowledge-proofs]] — ZKP for privacy-preserving AI audit
- [[Psychology/cognitive-biases]] — Behavioral design constraints in AI Act; manipulative AI prohibition
- [[Psychology/moral-psychology-and-ethical-decision-making]] — Ethical frameworks underlying AI governance norms
- [[Finance/quantitative-finance-and-algorithmic-trading]] — Algorithmic trading AI Act compliance; financial AI regulation
- [[Geopolitics/2026-05-27-us-china-great-power-competition]] — AI governance as strategic competition; Brussels Effect vs. China standards
- [[World Events/2026-05-30-global-ai-governance-race-to-regulate]] — 2026 current developments in global AI regulation
