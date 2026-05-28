---
title: Retrieval-Augmented Generation (RAG) — Architecture, Mechanics, and Best Practices
date: 2026-05-27
tags: [ai, llm, rag, retrieval, vector-databases, embeddings, nlp]
source: research
last_updated: 2026-05-28
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

## Related
- [[transformer-architecture]]
- [[machine-learning-fundamentals]]
- [[prompt-engineering]]
