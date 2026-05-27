---
title: Retrieval-Augmented Generation (RAG)
date: 2026-05-27
tags: [ai, llm, rag, vector-databases, information-retrieval]
source: research
last_updated: 2026-05-27
---

## Summary
Retrieval-Augmented Generation (RAG) is an AI architecture pattern that combines large language model generation with dynamic retrieval from an external knowledge base, solving the core limitations of LLMs: static training data, hallucination on specific facts, and inability to reason over private or recent information. Rather than embedding all knowledge in model weights, RAG queries a document store at inference time and injects relevant context into the prompt. It has become the dominant pattern for building production AI applications grounded in real-world data.

## Key Points
- LLMs hallucinate facts not well-represented in training data; RAG grounds responses in retrieved evidence
- The pipeline: query → embed → vector similarity search → retrieve chunks → inject into prompt → generate response
- Chunking strategy and embedding quality are the two biggest levers for retrieval performance
- Advanced patterns: HyDE, re-ranking, multi-hop retrieval, graph RAG, and agentic RAG
- Evaluation metrics: retrieval recall, answer faithfulness, answer relevance (RAGAs framework)

## Details

### Why RAG Exists: The LLM Knowledge Problem

Large language models encode knowledge in their weights during training. This creates fundamental limitations:

1. **Staleness**: Training data has a cutoff date. GPT-4's knowledge stops at a fixed date; it cannot know yesterday's news, your company's latest product specs, or a paper published last month.
2. **Hallucination**: LLMs generate statistically plausible text, not verified facts. When asked about specific details not in training data, they confabulate confidently.
3. **Privacy and proprietary data**: You cannot fine-tune a public LLM on your company's internal documents for every use case — it's expensive, slow, and creates IP exposure risks.
4. **Context window limits**: Even with large context windows (128K–1M tokens), loading an entire document corpus into every prompt is impractical.

RAG solves all four by separating *storage* from *generation*: knowledge lives in a queryable database; the LLM is the reasoning engine.

### The Basic RAG Pipeline

```
User Query
    ↓
[Embedding Model] → Query Vector
    ↓
[Vector Database] ← Document Embeddings (pre-indexed)
    ↓
Top-K Similar Chunks Retrieved
    ↓
[Prompt Construction] — Query + Retrieved Context + Instructions
    ↓
[LLM] → Generated Response (grounded in retrieved docs)
```

**Step 1: Indexing (offline)**
- Split documents into chunks (typically 200–1000 tokens)
- Embed each chunk using an embedding model (OpenAI text-embedding-3, Cohere embed, BGE, etc.)
- Store embeddings + metadata in a vector database (Pinecone, Weaviate, Chroma, pgvector, etc.)

**Step 2: Retrieval (at inference)**
- Embed the user's query with the same embedding model
- Run approximate nearest neighbor (ANN) search against the vector database
- Retrieve top-K most semantically similar chunks (typically K=3–10)

**Step 3: Generation (at inference)**
- Construct a prompt: `[System instructions] + [Retrieved chunks] + [User query]`
- Send to LLM
- LLM generates a response grounded in the retrieved evidence

### Chunking Strategies
Chunking is often the most impactful design decision in a RAG system.

**Fixed-size chunking**: Split every N tokens with some overlap. Simple but can split mid-sentence or mid-concept.

**Semantic chunking**: Split at natural boundaries (paragraphs, sections, sentence groups with similar embedding). Higher quality but more complex.

**Hierarchical chunking**: Index both large parent chunks (for context) and small child chunks (for precise retrieval). Retrieve small chunks but pass large chunks to the LLM.

**Document-structure chunking**: Respect the document's structure (headings, tables, code blocks). Essential for technical documentation.

**Key chunking considerations**:
- Chunks must be self-contained enough to be useful without surrounding context
- Overlap (10–20% typically) prevents important information from falling at boundaries
- Metadata (source, date, section, page) should be attached to every chunk

### Embedding Models
Embeddings map text to dense vectors in high-dimensional space (typically 768–3072 dimensions) such that semantically similar text is close together.

Popular embedding models:
- **OpenAI text-embedding-3-small/large**: High quality, API-based, ~$0.02/million tokens
- **Cohere Embed v3**: Strong multilingual support
- **BGE (Beijing Academy AI)**: Strong open-source models, can run locally
- **Jina Embeddings**: Good for long documents (8K token context)
- **Nomic Embed**: Open-source, competitive with commercial options

**Critical**: Always use the same embedding model for both indexing and query-time. Mixing models breaks the semantic space.

### Vector Databases
Specialized databases that index and query high-dimensional vectors efficiently using ANN algorithms (HNSW, IVF, LSH).

| Database | Best For |
|----------|---------|
| **Pinecone** | Managed SaaS, easiest to get started |
| **Weaviate** | Hybrid search (vector + keyword), self-hosted or cloud |
| **Chroma** | Local/embedded, great for prototyping |
| **pgvector** | PostgreSQL extension, ideal if you already use Postgres |
| **Qdrant** | High performance, rich filtering, open-source |
| **Milvus** | Large-scale enterprise deployments |
| **FAISS** | Facebook's library, best for pure in-memory ANN |

Most vector databases support **hybrid search**: combining dense vector similarity with sparse keyword (BM25) matching. This often outperforms pure vector search, especially for proper nouns and technical terms.

### Advanced RAG Patterns

**HyDE (Hypothetical Document Embeddings)**
Instead of embedding the query directly, ask the LLM to generate a hypothetical answer, then embed that. The hypothesis often sits closer in embedding space to real answers than a short question does. Improves retrieval for complex queries.

**Re-ranking**
Initial vector retrieval returns the top-K (e.g., 20) candidates by embedding similarity. A cross-encoder re-ranker (e.g., Cohere Rerank, BGE-reranker) then scores each candidate much more precisely against the query and re-orders them. Pass only the top-3 to the LLM. Significantly improves precision at a small latency cost.

**Multi-hop Retrieval**
For complex questions requiring synthesis across multiple documents, perform iterative retrieval: retrieve, generate an intermediate answer, use that to formulate a new query, retrieve again. Used in systems like IRCoT (Interleaved Reasoning and Retrieval).

**Graph RAG**
Build a knowledge graph from documents (entity extraction, relationship mapping). Retrieve via graph traversal rather than just vector similarity. Especially powerful for questions about relationships between entities. Microsoft's GraphRAG is the leading open-source implementation.

**Agentic RAG**
Give an LLM agent tools to perform retrieval as needed within a reasoning loop (ReAct, function calling). The agent decides when to search, what to search for, and how many times to iterate. More flexible and powerful than pipeline RAG for complex tasks.

### Evaluation: The RAGAs Framework
RAG systems are notoriously hard to evaluate. The RAGAs framework defines four core metrics:

1. **Context Recall**: Did the retrieval surface all necessary information? (Reference needed)
2. **Context Precision**: Of retrieved chunks, how many were actually relevant?
3. **Answer Faithfulness**: Does the generated answer stick to what the retrieved context says? (Hallucination detection)
4. **Answer Relevance**: Does the answer actually address the question asked?

Good RAG systems optimize all four. Common failure modes:
- Low recall: Key information wasn't retrieved (chunking or embedding problem)
- Low faithfulness: LLM ignored retrieved context and hallucinated
- Low relevance: LLM answered something adjacent but not the actual question

### RAG vs. Fine-tuning
A common question: should I RAG or fine-tune?

| | RAG | Fine-tuning |
|---|-----|-------------|
| **Best for** | Dynamic/changing knowledge, specific facts, private data | Style, format, behavior, reasoning patterns |
| **Update cost** | Re-index documents (cheap, fast) | Re-train model (expensive, slow) |
| **Interpretability** | High — can cite sources | Low — knowledge is baked into weights |
| **Hallucination** | Reduced (grounded in retrieved text) | May still hallucinate |
| **Setup cost** | Medium | High |

In practice, the best systems combine both: fine-tune for behavior and style, use RAG for factual grounding.

### Production Considerations
- **Latency**: Retrieval adds ~100–500ms. Can be parallelized with LLM call for some architectures.
- **Security**: Inject retrieved content carefully — prompt injection via malicious documents is a real attack vector.
- **Cost**: Embedding API calls and vector database hosting add to total cost. Monitor chunk sizes.
- **Freshness**: Set up incremental indexing pipelines to keep the vector database current.
- **Source attribution**: Always return source metadata with responses — critical for trust in enterprise applications.

## Related
- [[transformer-architecture]]
- [[machine-learning-fundamentals]]
- [[prompt-engineering]]
- [[vector-databases-and-embeddings]]
