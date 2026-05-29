---
title: Retrieval-Augmented Generation (RAG) — Architecture, Mechanics, and Best Practices
date: 2026-05-27
tags: [ai, llm, rag, retrieval, vector-databases, embeddings, nlp]
source: research
last_updated: 2026-05-29
---

## Summary
Retrieval-Augmented Generation (RAG) is an architecture that enhances large language models (LLMs) by grounding their responses in dynamically retrieved external documents rather than relying solely on parametric memory baked into weights at training time. First proposed by Lewis et al. (Meta AI, 2020), RAG has become the dominant pattern for building knowledge-intensive AI applications because it reduces hallucinations, keeps information current without retraining, and provides transparent sourcing.

## Key Points
- RAG = Retriever + Generator: a retrieval system finds relevant documents; an LLM reads them to produce grounded answers
- **Vector embeddings** encode semantic meaning numerically; cosine similarity retrieves the most relevant chunks
- The core pipeline: Query → Embed query → Vector search → Retrieve top-k chunks → Augment prompt → LLM generates answer
- RAG solves the **knowledge cutoff problem** and dramatically reduces hallucination rates on factual queries
- **Chunking strategy** is one of the highest-leverage decisions in RAG quality
- Advanced patterns: HyDE, re-ranking, parent-child chunking, query decomposition, recursive RAG

## Details

### Why RAG Exists: The Core Problem

LLMs learn facts from their training data and store them in their billions of parameters (hence "parametric memory"). This creates three problems:
1. **Knowledge cutoff**: the model knows nothing after its training date
2. **Hallucination**: when the model lacks a fact, it often confabulates a plausible-sounding answer
3. **No sourcing**: users cannot verify where a claim came from

The naive solution — retraining or fine-tuning the model on new data — is prohibitively expensive for most use cases and still doesn't provide citation-level traceability.

RAG solves this by externalizing knowledge. Instead of baking facts into weights, you retrieve them at inference time from a live, updateable knowledge base.

### Core Architecture

```
User Query
    │
    ▼
[Query Encoder / Embedding Model]
    │  (converts query to dense vector)
    ▼
[Vector Store / Index]  ◄─── Pre-indexed Documents
    │  (ANN search: returns top-k most similar chunks)
    ▼
[Retrieved Chunks (Context)]
    │
    ▼
[Prompt Assembly]
    │  "Answer the question using the context below: <chunks> \n\n Question: <query>"
    ▼
[LLM Generator]
    │
    ▼
[Grounded Response + Citations]
```

### Step 1: Document Ingestion and Chunking

Before retrieval can happen, documents must be indexed. The pipeline:

1. **Load** raw documents (PDFs, web pages, databases, APIs)
2. **Clean** (strip HTML, normalise whitespace)
3. **Chunk** — split into segments the LLM can process
4. **Embed** each chunk using an embedding model
5. **Store** in a vector database

**Chunking strategies** — arguably the most important RAG design decision:

| Strategy | How it works | Best for |
|---|---|---|
| **Fixed-size (token) chunking** | Split every N tokens with overlap | Simple baseline |
| **Sentence/paragraph splitting** | Split at natural boundaries | General text |
| **Semantic chunking** | Group sentences by semantic similarity | Heterogeneous documents |
| **Parent-child chunking** | Small chunks for retrieval, large parent chunks for context | Precise retrieval + full context |
| **Recursive character splitting** | Try multiple separators hierarchically | Code, structured text |

**Overlap**: adding 10–20% overlap between chunks prevents answers spanning chunk boundaries from being missed.

### Step 2: Embedding Models

An embedding model converts text to a dense vector (e.g., 1536 dimensions). Similar texts have vectors with high cosine similarity.

Popular embedding models:
- **OpenAI text-embedding-3-small/large**: strong general-purpose performance
- **Cohere Embed v3**: multilingual, efficient
- **BGE / E5 (BAAI)**: high-performing open-source options
- **Jina Embeddings**: long-context specialised
- **Voyage AI**: often top-of-leaderboard on MTEB benchmarks

Key properties: **context length** (how much text per chunk), **dimensionality** (higher = more expressive but more storage), **domain specificity** (code, legal, medical embeddings exist).

### Step 3: Vector Databases

Vector databases store embeddings and perform Approximate Nearest Neighbour (ANN) search at scale.

| Database | Notes |
|---|---|
| **Pinecone** | Fully managed, production-grade |
| **Weaviate** | Open source, strong hybrid search |
| **Qdrant** | Open source, Rust-based, fast |
| **Chroma** | Lightweight, great for development |
| **pgvector** | Postgres extension — vectors inside existing DB |
| **FAISS** | Meta's in-memory library, not a full DB |
| **Milvus** | Enterprise open-source scale |

**Hybrid search**: combining dense vector search (semantic) with sparse BM25 keyword search often outperforms either alone. Useful when exact term matching matters (proper nouns, SKUs, codes).

### Step 4: Retrieval Quality

Metrics:
- **Recall@k**: percentage of truly relevant documents in the top-k results
- **Precision@k**: percentage of retrieved documents that are actually relevant
- **MRR (Mean Reciprocal Rank)**: how early the first relevant result appears
- **NDCG**: accounts for graded relevance and rank position

**Re-ranking**: a second-stage model (e.g., Cohere Reranker, cross-encoder) scores the top-k retrieved chunks for relevance to the specific query. Computationally expensive but significantly improves precision.

### Step 5: Prompt Augmentation and Generation

The retrieved chunks are injected into the prompt context window:

```
System: You are a helpful assistant. Answer using ONLY the context provided. 
If the answer is not in the context, say "I don't know."

Context:
[Chunk 1 text]
---
[Chunk 2 text]
---
[Chunk 3 text]

User: What is the refund policy?
```

**Critical design choices:**
- **Context window management**: with many chunks, prioritise most relevant, or use a "lost in the middle" mitigation strategy (most important content at start and end)
- **Citation instructions**: instruct the LLM to cite specific chunks (enables verifiability)
- **Handling "no answer found"**: explicit instruction to abstain reduces hallucination

### Advanced RAG Patterns

**HyDE (Hypothetical Document Embeddings)**: instead of embedding the query directly, use the LLM to generate a hypothetical answer, then embed that. The hypothetical answer's embedding is closer in space to real document embeddings, improving recall.

**Query Decomposition / Multi-query retrieval**: break complex queries into sub-questions, retrieve for each, then synthesise. Improves coverage for multi-part questions.

**Agentic RAG / Self-RAG**: the LLM decides whether to retrieve, what to retrieve, and whether retrieved content is sufficient. Allows iterative retrieval until confidence threshold is met.

**Recursive retrieval (RAG over RAG)**: first retrieve a summary index to identify relevant document sections, then retrieve fine-grained chunks only from those sections — reduces noise.

**RAPTOR**: hierarchically cluster and summarise document chunks, enabling retrieval at multiple levels of abstraction.

### Evaluation Framework

RAG quality is assessed at three levels:

| Component | What to measure | Tool |
|---|---|---|
| **Retrieval** | Are the right chunks retrieved? | Recall@k, NDCG |
| **Generation** | Is the answer faithful to retrieved context? | Faithfulness (RAGAS) |
| **End-to-end** | Is the final answer correct? | Answer Relevancy, F1 |

**RAGAS** (RAG Assessment) is the dominant open-source framework. Key metrics:
- **Faithfulness**: does the answer stick to retrieved context (no hallucination)?
- **Answer Relevancy**: how relevant is the answer to the question?
- **Context Precision/Recall**: did retrieval find the right and complete context?

### When NOT to Use RAG

RAG is powerful but not always the right tool:
- **Structured queries over databases** → use Text-to-SQL instead
- **Simple FAQ matching** → fine-tuned classifier or keyword search may be faster
- **Real-time computation** → RAG retrieves documents, not live APIs; use function calling
- **Very long document reasoning** → sometimes full-document context (very long context LLMs) beats RAG
- **Low-latency applications** → each RAG call adds latency (embedding + retrieval + reranking)

### Production Considerations

- **Embedding model consistency**: you MUST use the same embedding model at indexing and query time; changing models requires full re-indexing
- **Chunk version control**: when documents update, delta-index only changed chunks
- **Security**: filter retrieval results by user access permissions before injecting into prompt (authorization must happen at retrieval layer)
- **Observability**: log every retrieval call — what was retrieved, what was generated, user feedback — to continuously improve

### GraphRAG (Microsoft, 2024)

Standard vector RAG retrieves semantically similar chunks but misses **cross-document relationships** — facts that require synthesizing information across many disparate sources.

**GraphRAG** builds a knowledge graph from the document corpus before retrieval:
1. Extract entities and relationships from all documents using LLMs
2. Build a graph with entities as nodes, relationships as edges
3. Cluster graph communities (Leiden algorithm)
4. Summarize each community and its key themes
5. At query time, retrieve both: community summaries (for broad context) + specific entity chunks (for precise answers)

**When GraphRAG outperforms standard RAG**:
- Queries that ask "what are the main themes across all documents?"
- Questions requiring multi-hop reasoning: "How did X influence Y through Z?"
- Entity-centric queries across a large, heterogeneous document set

**Tradeoff**: GraphRAG is expensive to build (LLM calls for entity extraction on every document) and slower to query. It is overkill for simple factual Q&A but transformative for complex analytical workloads over large corpora.

---

### Corrective RAG (CRAG)

Standard RAG trusts retrieved chunks even when they're irrelevant or incorrect. **Corrective RAG** (Yan et al., 2024) adds a validation and correction step:

1. Retrieve top-k chunks (standard)
2. **Evaluate relevance** of retrieved chunks using a lightweight classifier
3. If chunks are high-quality → use directly
4. If chunks are ambiguous → supplement with web search
5. If chunks are entirely irrelevant → discard and rely on web search + knowledge distillation

This dynamic correction loop reduces hallucinations from poor retrieval without requiring a full re-engineering of the retrieval pipeline.

---

### Long-Context LLMs vs. RAG

As LLM context windows have expanded (GPT-4 128K → Gemini 1.5 1M → Claude 3.5 200K+), the question arises: can you simply throw the entire document corpus into the context and skip RAG entirely?

**When full-context beats RAG**:
- The corpus fits in the context window
- Reasoning requires holistic understanding (not specific fact retrieval)
- Very long novels, codebases, or legal documents where any chunk might be relevant

**When RAG beats full-context**:
- Corpus is too large for any context window
- Low-latency is required (vector search is faster than attention over millions of tokens)
- Cost efficiency (filling 1M token context is extremely expensive per query)
- Dynamic, updateable knowledge bases (can't update a static context)

**The emerging hybrid**: Use RAG to retrieve the top candidates, then use a long-context model to reason over the full retrieved content without further chunking — "RAG over long context." Combines RAG's scalability with full-document reasoning.

---

### RAG Evaluation with LLM-as-Judge

Human evaluation of RAG systems is slow and expensive. **LLM-as-judge** automates evaluation at scale:

**RAGAS framework** scores:
- **Faithfulness**: Does the answer make claims that are actually supported by the retrieved context? (Detected by asking the LLM: "Can each claim in this answer be traced to the context?")
- **Answer Relevancy**: Does the answer address the actual question? (LLM generates hypothetical questions from the answer; cosine similarity with original question)
- **Context Recall**: Is all necessary information present in the retrieved context? (Requires ground truth)
- **Context Precision**: How much irrelevant content was retrieved?

**G-Eval** (NLG evaluation): Multi-step LLM evaluation prompting the model to reason through specific evaluation criteria before scoring. Outperforms simple scoring prompts.

**Pitfall**: LLM judges have systematic biases (prefer longer answers, prefer their own outputs, sensitive to presentation order). Mitigation: use multiple judge models, swap answer order, evaluate specific dimensions separately.

---

### Modular RAG: Compose, Don't Inherit

The field has moved beyond the monolithic RAG pipeline toward **modular RAG** — compose specialized modules for each stage:

- **Retrieval module**: swap vector search, BM25, SPLADE, or GraphRAG depending on query type
- **Refinement module**: re-ranking, compression, or filtering
- **Generation module**: choose LLM based on task (cheap small model for simple Q&A; large model for complex synthesis)
- **Memory module**: episodic memory (conversation history), semantic memory (user preferences), procedural memory (which tools/modules to use when)

This composable architecture — exemplified by frameworks like LlamaIndex, Haystack, and LangChain — enables production systems to route queries to the appropriate sub-pipeline rather than applying the same approach to every query.

### Self-RAG — Adaptive, Self-Reflective Retrieval

Standard RAG always retrieves, even for queries where retrieval is unnecessary or harmful (e.g., asking "what is 2+2?" triggers a retrieval call that adds noise). **Self-RAG** (Asai et al., 2023) trains the model to:

1. **Decide whether to retrieve**: Generate a special `[Retrieve]` or `[No Retrieve]` token before answering. Retrieval only happens if the model determines it needs external grounding.
2. **Evaluate retrieved passages**: Generate `[Relevant]` / `[Partially Relevant]` / `[Irrelevant]` tokens for each retrieved passage.
3. **Assess its own output**: Generate `[Fully Supported]` / `[Partially Supported]` / `[No Support]` tokens rating whether its answer is grounded in retrieved content.
4. **Critique overall quality**: Generate `[Utility: 1–5]` score to distinguish high-quality supported answers from hedged or incomplete ones.

**What this achieves**: The model becomes a self-regulating RAG system — it retrieves only when needed, filters bad retrievals, and rates its own faithfulness. At inference, you can weight retrieval vs. self-reliance and faithfulness vs. utility according to the application's needs.

**Training requirement**: Self-RAG requires specialized fine-tuning on data that includes these reflection tokens. It is not a prompting technique applied to off-the-shelf models.

---

### Multimodal RAG

Standard RAG handles text; **multimodal RAG** extends retrieval to images, tables, charts, diagrams, and audio.

**The challenge**: Embedding text and images into the same vector space so a text query can retrieve relevant images (and vice versa).

**Approaches**:

| Approach | How it works | Trade-off |
|----------|-------------|-----------|
| **CLIP-based retrieval** | CLIP embeds text and images in the same space; cosine similarity works cross-modally | Good zero-shot performance; CLIP embeddings less rich than specialized text embeddings |
| **Caption-then-retrieve** | Use a vision model to caption images; embed and index captions as text | Simple to implement; loses fine-grained visual detail |
| **Multi-modal embeddings (ColPali)** | Embed full page images as tokens; late interaction retrieval | State-of-the-art for document retrieval; high storage cost |
| **Table/chart extraction** | Parse tables to structured data or text descriptions; retrieve as structured content | Accurate for numbers; requires specialized parsers |

**ColPali** (Faysse et al., 2024): Instead of extracting text from PDFs, ColPali embeds the full visual representation of each PDF page as a sequence of patch embeddings (using PaliGemma). Retrieval uses late-interaction scoring (similar to ColBERT but over image patches). Achieves state-of-the-art on document question answering benchmarks by avoiding OCR errors entirely.

**Production pattern for mixed document corpora**:
1. Parse documents → extract text blocks, tables, and images separately
2. Text blocks → standard text embedding pipeline
3. Images/charts → caption with vision model, store both caption embedding and raw image
4. At retrieval: retrieve text chunks AND relevant images; inject both into the multimodal LLM's context

---

### RAG vs. Fine-Tuning: Decision Framework

The choice between RAG and fine-tuning is one of the most consequential architectural decisions in applied LLM work. They are often presented as alternatives, but they address different problems.

| Dimension | RAG | Fine-Tuning |
|-----------|-----|-------------|
| **Problem solved** | Access to external knowledge the model doesn't have | Style, format, specialized reasoning, domain behavior |
| **Knowledge currency** | Real-time — update the index without retraining | Static — knowledge frozen at training time |
| **Traceability** | Citable sources from retrieved chunks | Opaque — no way to trace where knowledge came from |
| **Cost to update** | Low — just re-index changed documents | High — full fine-tuning run required |
| **Latency** | Higher — retrieval + inference | Standard — just inference |
| **What it changes** | What the model knows at query time | How the model behaves / reasons |
| **Failure mode** | Poor retrieval → poor answer | Catastrophic forgetting; overfitting |

**Decision heuristics**:

- **Use RAG when**: The use case requires up-to-date or proprietary knowledge; you need citations; the knowledge base changes frequently; you want to control exactly what information the model can access.

- **Use fine-tuning when**: You need the model to adopt a specific output format or persona consistently; you're adapting to a specialized domain where base model reasoning patterns are inadequate; you want to inject frequently-used patterns into weights to reduce prompt overhead; you need latency too low to afford retrieval.

- **Use both when**: Fine-tune for domain behavior and format; RAG for dynamic factual knowledge. This is the pattern used in enterprise deployments: a fine-tuned model with domain vocabulary and communication style, augmented with a RAG system for current company data.

**The fine-tuning trap**: Many teams fine-tune to inject factual knowledge (e.g., "add our product catalog to the model"). This rarely works well — facts are hard to reliably inject via fine-tuning and easy to retrieve via RAG. Fine-tuning is most effective for *behavioral* changes, not *knowledge* injection.

---

### Historical Development

RAG synthesises information retrieval (a field dating to the 1960s) with neural language generation — two traditions that developed largely in parallel until 2020.

**1960s–1990s — Classical Information Retrieval**: The Cranfield experiments (1960–1966, UK) established evaluation frameworks for document retrieval systems. TF-IDF (term frequency–inverse document frequency) becomes the dominant relevance scoring function, formalised by Karen Spärck Jones (1972) and extended by Robertson & Ogilvie (BM25, 1994). These sparse keyword-matching approaches underpin Google Search's early architecture and remain competitive for many retrieval tasks through the present.

**1990s–2000s — Question Answering Systems**: IBM's Watson (built 2010, won Jeopardy 2011) represents the peak of pre-neural QA: a pipeline of 100+ handcrafted components — named entity recognition, passage retrieval, semantic role labelling, answer scoring — operating over a structured knowledge base and Wikipedia. Watson retrieves relevant passages and synthesises answers from them — a conceptual ancestor of RAG without neural components.

**2017 — Dense Passage Retrieval Experiments**: With BERT's release (2018), researchers begin replacing sparse BM25 retrieval with dense neural embeddings. Experiments show that BERT-based bi-encoders significantly outperform BM25 on the Natural Questions benchmark for the first time.

**2019 — Dense Passage Retrieval (DPR, Facebook AI)**: Karpukhin et al. demonstrate that DPR trained on question-passage pairs dramatically outperforms BM25 on Natural Questions and TriviaQA — closing the gap between retrieval quality and downstream QA performance. DPR introduces the bi-encoder architecture (separate encoders for query and document, trained with in-batch negatives) that underlies all modern embedding models.

**September 2020 — RAG (Lewis et al., Meta AI)**: Patrick Lewis, Ethan Perez, Aleksandra Piktus, Fabio Petroni, Vladimir Karpukhin, Naman Goyal, Heinrich Küttler, Mike Lewis, Wen-tau Yih, Tim Rocktäschel, Sebastian Riedel, and Douwe Kiela publish "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks" (NeurIPS 2020). Key contributions:
1. End-to-end differentiable retrieval: the retriever (DPR) and generator (BART) are jointly fine-tuned
2. Marginalisation over retrieved documents: generate answers that are consistent with multiple retrieved passages
3. State-of-the-art on Natural Questions, TriviaQA, and fact verification without any task-specific fine-tuning
This paper coins the term "RAG" and establishes the architecture.

**2021 — REALM and the Pre-Training Era**: Guu et al. (Google) publish REALM: Retrieval-Enhanced Language Model Pretraining — a system where retrieval is integrated into the pretraining process itself. REALM pretrained a model to retrieve Wikipedia passages as part of masked language modelling. While REALM is computationally expensive and superseded, it establishes the principle that retrieval can be deeply integrated, not just bolted on.

**2022 — Scaling and Productisation**:
- Langchain (October 2022): Harrison Chase releases the first RAG orchestration framework, reaching 10,000 GitHub stars within weeks. Provides abstractions for chunking, embedding, retrieval, and generation that make RAG accessible to developers without ML expertise.
- Llama Index (October 2022, originally GPT Index): Jerry Liu releases a competing framework focused specifically on RAG architectures over complex document corpora.

**2023 — Advanced RAG Patterns Proliferate**:
- HyDE (Gao et al., May 2023): Hypothetical Document Embeddings — generate a fake answer, retrieve documents similar to the fake answer. +5–15% recall on TREC and MS MARCO.
- Self-RAG (Asai et al., October 2023, CMU/UW/Allen Institute): Fine-tune models to generate special reflection tokens that control retrieval and self-evaluate quality
- GraphRAG (Microsoft, November 2023): Knowledge graph extraction + community summarisation for complex analytical queries

**2024 — Multimodal, Long-Context, and Production at Scale**:
- ColPali (February 2024): Multi-vector visual document retrieval — embed full PDF page images without OCR
- Corrective RAG (January 2024): Adaptive fallback to web search when retrieved documents are low quality
- The emergence of 128K–1M token context windows challenges the assumption that RAG is always necessary for large corpora

**2025–2026 — Enterprise RAG at Scale**: Most Fortune 500 companies have RAG deployments in production. Benchmarks show that enterprise RAG deployments with well-structured chunking and re-ranking reduce hallucination rates from ~40–60% (base LLM on knowledge-intensive queries) to ~5–15%, making the technology viable for customer-facing applications.

---

### Mathematical Foundation

#### Vector Similarity Search
The core retrieval operation: given a query embedding **q** ∈ ℝ^d and a corpus of N document embeddings {**d₁**, **d₂**, ..., **d_N**} ⊂ ℝ^d, find the top-k most similar documents.

**Cosine similarity** (most common for text):
```
sim(q, dᵢ) = (q · dᵢ) / (||q|| · ||dᵢ||)
```

If all vectors are L2-normalised (||v|| = 1), cosine similarity reduces to dot product — enabling efficient matrix multiplication: sim(**q**, **D**) = **q** · **Dᵀ**, where **D** ∈ ℝ^(N×d) is the matrix of document embeddings.

**BM25 scoring** (sparse baseline):
```
BM25(q, d) = Σ_{term t in q} IDF(t) · [TF(t,d) · (k₁+1)] / [TF(t,d) + k₁·(1-b+b·|d|/avgdl)]
```
Where:
- IDF(t) = log[(N - df_t + 0.5) / (df_t + 0.5) + 1] — penalises common terms
- TF(t,d) = count of term t in document d
- k₁ ∈ [1.2, 2.0] — term saturation parameter (default 1.5)
- b ∈ [0, 1] — length normalisation (default 0.75)
- avgdl = average document length in tokens

**Reciprocal Rank Fusion (RRF)** — combining dense and sparse scores:
```
RRF_score(d) = Σ_{ranker r} 1 / (k + rank_r(d))
```
Where k = 60 (empirically robust constant). For each ranked list (dense, sparse), each document gets a score inversely proportional to its rank. RRF is parameter-free, robust to scale differences between scorers, and empirically outperforms weighted combination in most benchmarks.

#### The RAGAS Evaluation Framework
RAGAS (Retrieval Augmented Generation Assessment) provides automated evaluation via LLM-as-judge:

**Faithfulness** — fraction of answer claims that can be traced to retrieved context:
```
Faithfulness = |{claims in answer that are supported by context}| / |{total claims in answer}|
```
Computed by prompting a judge LLM to decompose the answer into atomic claims and verify each claim against the retrieved chunks.

**Answer Relevancy** — semantic similarity between the actual question and questions that the answer implies:
```
AnswerRelevancy = (1/n) Σᵢ cos_sim(q, qᵢ')
```
Where qᵢ' are questions generated by an LLM from the answer alone. If the answer actually addresses the question, generated questions should be similar to the original.

**Context Precision@k**:
```
CP@k = (1/k) Σ_{i=1}^{k} [Precision@i · rel(i)]
```
Where rel(i) = 1 if the i-th retrieved chunk is relevant, 0 otherwise.

---

### Benchmarks and Performance

#### Hallucination Reduction
The primary motivation for RAG is hallucination reduction. Empirical measurements from production deployments:

| Setup | Hallucination Rate | Source |
|-------|-------------------|--------|
| GPT-4 (no retrieval, knowledge-intensive QA) | 38–52% | Various enterprise evals |
| GPT-3.5 (no retrieval) | 60–75% | Various benchmarks |
| RAG with basic chunking (text-embedding-ada-002 + GPT-4) | 12–20% | RAGAS benchmark studies |
| RAG with re-ranking (BGE-reranker + GPT-4) | 6–10% | Enterprise case studies |
| RAG with parent-child chunking + re-ranking | 4–8% | Advanced production benchmarks |
| Fine-tuned LLM on domain data (no RAG) | 15–25% | Fine-tuning typically doesn't eliminate hallucination |

#### BEIR Benchmark (Thakur et al., 2021)
BEIR is the standard retrieval quality benchmark — 18 diverse datasets including TREC News, Natural Questions, ArguAna, and BioASQ. Measured as NDCG@10 (normalised discounted cumulative gain at rank 10).

| Retriever | Avg NDCG@10 (BEIR) | Notes |
|-----------|-------------------|-------|
| BM25 | 42.8 | Surprisingly strong baseline |
| DPR (bi-encoder, fine-tuned NQ) | 33.8 | Generalises poorly out-of-domain |
| OpenAI text-embedding-3-large | 55.4 | Top commercial model |
| E5-large-v2 | 56.2 | Strong open-source |
| BGE-M3 | 57.0 | BAAI; multi-lingual, multi-granularity |
| Voyage-3 | 62.5 | Top leaderboard (MTEB) as of 2025 |
| BM25 + Dense (RRF hybrid) | ~60–63 | Consistently beats either alone |

#### Natural Questions (Open-Domain QA)
Lewis et al. (2020) RAG paper results vs. alternatives:

| Model | NQ Exact Match | TriviaQA EM |
|-------|---------------|-------------|
| T5-11B (no retrieval) | 34.5 | 37.4 |
| DPR + FiD (dense retrieval + reader) | 51.4 | 67.6 |
| RAG (Lewis et al. 2020) | 44.5 | 56.8 |
| RAG + RLHF fine-tuned model | 58.2 | 71.9 |
| GPT-4 + RAG (2023 systems) | ~65+ | ~80+ |

#### Production Latency
Typical RAG pipeline latency breakdown (cloud deployment, 2025):
- Query embedding (text-embedding-3-small): ~15ms
- Vector search (Pinecone, 1M vectors): ~20–40ms
- Re-ranking (Cohere Reranker v3, top 50→5): ~100–150ms
- LLM generation (GPT-4o, 500 output tokens): ~1.5–3s
- **Total end-to-end: ~2–4 seconds**

Without re-ranking: ~1.5–2.5 seconds. The re-ranking step adds ~15–25% latency but typically improves answer quality by 15–30%.

---

### Real-World Deployment Case Studies

**Morgan Stanley Wealth Management (2023)**: Deployed RAG over 100,000+ internal research documents and financial reports to help financial advisors retrieve precise answers. Using OpenAI embeddings + GPT-4, the system processes ~50,000 advisor queries per week. Reported 40% reduction in time advisors spend searching for information.

**Klarna Customer Service (2023)**: Deployed an LLM + RAG system over support documentation in 35 languages, handling 2.3 million customer conversations in the first month. Achieved equivalent satisfaction scores to human agents on ~67% of queries, reducing average resolution time from 11 minutes to 2 minutes.

**Harvey AI (Legal, 2023–2025)**: RAG over case law, statutory databases, and client documents for legal research and contract analysis. Used by major law firms including Allen & Overy, PwC Legal. Processes briefs, precedents, and discovery documents. The key technical challenge: legal documents use specialised terminology requiring domain-specific embedding fine-tuning.

**Notion AI (2023)**: RAG over a user's entire Notion workspace — notes, databases, documents — to answer personal knowledge questions. The engineering challenge: real-time indexing as documents are edited, with access control (only retrieve documents the user has permission to read).

**GitHub Copilot Chat (2023)**: RAG over the local code repository — embedding code files, function signatures, and documentation — so the chat assistant can answer questions about the specific codebase. Technical challenge: code embeddings must capture structural relationships (call graphs, imports) not just textual similarity.

## Cross-Disciplinary Connections

### RAG as an Institutional Memory Architecture

The fundamental problem RAG solves — a capable agent whose in-weights knowledge is static and subject to confident confabulation — is the same problem that institutional memory management has always faced. Organizations build knowledge management systems (intranets, wikis, document repositories) precisely because individual employees cannot hold all institutional knowledge in their heads, and because employee turnover means that knowledge baked into individuals eventually walks out the door. RAG is, at the system architecture level, a formalization of how well-functioning organizations actually work: a capable generalist (the LLM) augmented by a well-organized, searchable document repository (the vector store) with clear protocols for when to look something up versus when to rely on existing knowledge. The comparison illuminates both systems' failure modes: just as a consultant who ignores the document repository and relies on confident half-remembered knowledge produces wrong answers with high confidence, an LLM without RAG hallucinate precisely because it is optimized to produce confident completions. The [[mental-models]] literature on the Dunning-Kruger effect is directly applicable: the most dangerous failure mode is not knowing that you don't know, which is exactly the hallucination problem RAG addresses by forcing the model to ground answers in retrieved evidence.

### The Epistemology of Grounded Belief

Retrieval-augmented generation is, at a deeper level, an architectural implementation of a fundamental epistemological principle: beliefs should be grounded in evidence, and the source of a belief should be traceable. The philosophical tradition of justified true belief — going back to Plato's Theaetetus — holds that knowledge requires not just having the right answer but having it for the right reasons, with the ability to provide justification. RAG provides exactly this: each claim in the generated answer can, in principle, be traced to a specific retrieved chunk. This connects directly to the [[cialdini-influence]] principle of authority and social proof — when RAG systems surface citations to authoritative sources, they trigger users' compliance with the authority principle, and correctly so, because the citation is genuine. Contrast this with a pure LLM answer that claims authority while having none traceable source. The design question of how prominently to display citations is therefore not merely a UX decision but an epistemological and influence question: how much of the model's persuasive authority should rest on its presentation style versus the verifiable quality of its sources? The answer, from a principled epistemological standpoint, should always be the latter.

### RAG Infrastructure and the Economics of Knowledge

From a financial perspective, RAG represents a fundamental shift in the cost structure of deploying knowledge-intensive AI systems. The traditional alternative — fine-tuning a large model on domain-specific knowledge — is a capital expenditure: a large upfront cost that embeds knowledge into weights, where it is both expensive to update and impossible to audit. RAG converts knowledge storage into an operating expenditure: a vector database that can be updated incrementally, queried at cost proportional to usage, and audited because the retrieved chunks are explicit. This cost structure parallels the broader shift from owning to renting infrastructure analyzed in cloud economics, and it has the same strategic implications. Organizations that fine-tune proprietary models are making an illiquid knowledge investment — the knowledge is locked in weights, expensive to update, and potentially lost if the model is deprecated. Organizations that invest in well-structured RAG pipelines are building liquid knowledge assets — structured, updateable document repositories that will retain value regardless of which LLM generation they are paired with. The [[valuation-fundamentals]] insight that assets should be valued based on future cash flows rather than replacement cost applies here: a well-structured, semantically indexed document repository has compounding value because every new LLM generation can use it immediately.

### The Lost in the Middle Effect and Organizational Attention

The "lost in the middle" phenomenon — where LLMs systematically underweight information positioned in the middle of long context windows — has a direct analog in organizational behavior and cognitive psychology. In organizational settings, information buried in the middle of long reports, agendas, or email threads is systematically underattended, a pattern analyzed under the heading of primacy and recency effects in [[cognitive-biases]]. The practical design response is identical in both cases: structure the most critical information at the beginning and end of any information sequence. The RAG-specific mitigation of re-ordering retrieved chunks to place the most relevant content first and last is, in cognitive terms, a form of information architecture that fights the primacy/recency attention distribution — the same principle that explains why presentations should open with the key insight rather than building to it, and why legal briefs put the conclusion first. Advanced RAG patterns like parent-child chunking and RAPTOR's hierarchical summarization are attempts to solve this architecturally — rather than fighting the model's attention distribution, they restructure the information so the most important signals are always in the high-attention positions.

### RAG, Verification, and the Epistemics of Influence

Robert Cialdini's analysis of social proof in [[cialdini-influence]] identifies a crucial asymmetry: people are more influenced by evidence that appears verifiable than by evidence that is merely stated confidently. RAG systems, by grounding answers in citable retrieved documents, exploit this asymmetry in the direction of genuine epistemic quality — users can verify the claim by reading the cited source. However, the same architecture creates a new class of manipulation risk: a RAG system pointed at a carefully curated corpus of misleading-but-authoritative-looking documents will produce answers that are verifiably traceable to sources while remaining systematically wrong. This is the AI-era version of the classic propaganda technique of citation laundering — citing sources that themselves cite other sources in a circular supporting structure. The security implication for RAG system designers is that the trust model for the vector store is as important as the trust model for the LLM: the authorization layer that controls which documents enter the index is the most critical security boundary in the entire system, more so than prompt injection defense, because a corrupted index will produce confidently wrong answers with legitimate-looking citations.

### Hybrid RAG and the Efficient Markets Analogy

The debate between pure dense vector search, pure sparse BM25 search, and hybrid approaches in RAG mirrors the debate between fundamental analysis and technical analysis in financial markets — analyzed in [[technical-analysis-and-chart-patterns]] and [[value-investing-methodology]]. Dense semantic search finds documents that are conceptually related to the query even when no keywords overlap — analogous to fundamental analysis identifying undervalued companies through qualitative assessment of business quality. Sparse BM25 retrieval finds documents that contain the exact terms of the query — analogous to technical analysis identifying momentum through price pattern matching. The well-documented empirical finding that hybrid dense+sparse retrieval consistently outperforms either alone is precisely analogous to the investment research finding that blending fundamental and quantitative signals outperforms either approach in isolation. Both findings suggest that different information-processing mechanisms capture different real signal, and that the combination is more than additive. The Reciprocal Rank Fusion algorithm that blends dense and sparse retrieval scores is, in this framing, the financial equivalent of a multi-factor model that combines value, momentum, and quality signals.

## Related
- [[transformer-architecture]]
- [[machine-learning-fundamentals]]
- [[prompt-engineering]]
- [[vector-databases-and-embeddings]]
- [[docker-and-containerization]]
- [[diffusion-models-and-image-generation]]
- [[cognitive-biases]]
- [[cialdini-influence]]
- [[mental-models]]
- [[valuation-fundamentals]]
- [[technical-analysis-and-chart-patterns]]
- [[value-investing-methodology]]
