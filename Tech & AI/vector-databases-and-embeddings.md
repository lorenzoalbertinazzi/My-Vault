---
title: Vector Databases and Embeddings
date: 2026-05-27
tags: [tech, AI, machine-learning, embeddings, vector-databases, semantic-search]
source: research
last_updated: 2026-05-27
---

## Summary
Embeddings are dense numerical representations of data (text, images, audio) that capture semantic meaning in high-dimensional vector space. Vector databases are purpose-built systems for storing and retrieving these embeddings efficiently via similarity search — the backbone infrastructure behind semantic search, recommendation engines, and Retrieval-Augmented Generation (RAG) pipelines. They represent a fundamental shift from keyword/SQL lookup to *meaning-based* data retrieval.

## Key Points
- An embedding is a list of floating-point numbers (e.g., 1,536 dimensions for `text-embedding-3-small`) encoding semantic meaning
- Semantically similar items cluster together in vector space — "dog" and "puppy" are geometrically near each other
- **Cosine similarity** is the most common distance metric for text embeddings
- Vector databases use **Approximate Nearest Neighbor (ANN)** algorithms (especially HNSW) for fast retrieval at scale
- Major vector databases: **Pinecone**, **Weaviate**, **Qdrant**, **Chroma**, **Milvus**, **pgvector** (PostgreSQL extension)
- Critical component of RAG (Retrieval-Augmented Generation) — allows LLMs to query relevant context at inference time
- Traditional SQL databases cannot efficiently handle high-dimensional similarity search

## Details

### What Is an Embedding?

An **embedding** is a mathematical transformation of data into a fixed-length vector of real numbers. The transformation is learned by a neural network trained on massive datasets, such that:
- Items that are **semantically similar** produce vectors that are **geometrically close**
- Items that are **semantically different** produce vectors that are **geometrically distant**

Example in 2D (simplified):
```
"dog"   → [0.82, 0.15]
"puppy" → [0.79, 0.18]   ← close to "dog"
"car"   → [0.12, 0.91]   ← far from "dog"
```

Real embeddings operate in hundreds or thousands of dimensions. OpenAI's `text-embedding-3-large` produces 3,072-dimensional vectors. Google's `text-embedding-004` outputs 768 dimensions.

### How Embeddings Are Created

Embedding models are neural networks (usually Transformer-based) trained with **contrastive learning** objectives:
- **Positive pairs** (semantically similar sentences) are trained to produce similar vectors
- **Negative pairs** (semantically unrelated) are trained to produce dissimilar vectors
- Training datasets include semantic textual similarity benchmarks, paraphrase databases, and search engine query-document pairs

Popular embedding models:
| Model | Dimensions | Provider | Notes |
|-------|------------|----------|-------|
| text-embedding-3-small | 1,536 | OpenAI | Cost-effective |
| text-embedding-3-large | 3,072 | OpenAI | Higher quality |
| text-embedding-004 | 768 | Google | Strong multilingual |
| embed-v3.0 | 1,024 | Cohere | Strong for retrieval |
| all-MiniLM-L6-v2 | 384 | Sentence-Transformers | Open-source, fast |
| nomic-embed-text | 768 | Nomic | Open-source, long context |

### Similarity Metrics

When you query a vector database, you're asking: "Which stored vectors are most similar to this query vector?"

**Cosine Similarity** (most common for text):
```
similarity = (A · B) / (|A| × |B|)
```
Ranges from -1 (opposite) to 1 (identical). Measures the angle between vectors, ignoring magnitude. Ideal when you care about direction/meaning, not scale.

**Dot Product**: Similar to cosine but affected by vector magnitude. Preferred when embeddings are normalized (magnitude = 1), in which case it equals cosine similarity.

**Euclidean Distance (L2)**: Straight-line distance in high-dimensional space. More sensitive to vector scale. Common in image embeddings.

### The Scalability Problem: Why Standard Databases Fail

If you have 10 million documents stored as vectors, finding the top-10 most similar to a query by checking all 10 million (brute-force exact search) is O(n × d) — too slow for real-time applications at scale.

Solution: **Approximate Nearest Neighbor (ANN)** algorithms that trade a small accuracy loss for massive speed gains.

#### HNSW — Hierarchical Navigable Small World (the dominant algorithm)
HNSW builds a multi-layer graph structure during indexing:
- **Bottom layer**: all vectors, densely connected to near-neighbors
- **Higher layers**: progressively sparser "highway" graphs for fast traversal
- **Query**: starts at the top (sparse) layer to quickly navigate to the right neighborhood, then descends for precise local search

HNSW achieves sub-millisecond search over millions of vectors with ~95%+ recall. It is the default index type in most production vector databases.

Other ANN approaches: IVF (Inverted File Index), ScaNN (Google), DiskANN (for disk-resident data at billion-scale).

### Vector Database Landscape

| Database | Type | Best For | Notes |
|----------|------|----------|-------|
| **Pinecone** | Managed cloud | Production, scale, simplicity | No infrastructure management |
| **Weaviate** | Open-source / cloud | Multi-modal, graph + vector | Built-in modules (LLM, image) |
| **Qdrant** | Open-source / cloud | Rust-based, payload filtering | Fast, strong filtering on metadata |
| **Chroma** | Open-source | Development, prototyping | Python-native, simple API |
| **Milvus** | Open-source | Billion-scale enterprise | Most mature open-source option |
| **pgvector** | PostgreSQL extension | Existing PostgreSQL users | SQL + vector in one system |
| **Redis VSS** | Redis module | Low-latency, in-memory | Combines caching + vector search |
| **LanceDB** | Open-source | Embedded, serverless | Built on Apache Lance format |

### Metadata Filtering: The Critical Feature
Raw vector similarity search returns results "semantically close" but may ignore hard constraints. A real-world query might be: "Find documents similar to this query, BUT only from user_id=42, created after 2025-01-01, in the 'finance' category."

Vector databases support **pre-filtering** (filter before ANN search) and **post-filtering** (filter after ANN search) on metadata fields. This hybrid search capability is essential for production use cases.

### The Embedding Pipeline (Production Pattern)
```
[Raw Data] → [Chunking] → [Embedding Model] → [Vector DB]
                                                      ↓
[User Query] → [Embed Query] → [ANN Search] → [Top-K Results] → [LLM / App]
```

Key decisions in this pipeline:
- **Chunk size**: Smaller chunks = more precise retrieval; larger chunks = more context. Common sizes: 256–1024 tokens
- **Overlap**: Sliding window overlap between chunks prevents context loss at chunk boundaries
- **Re-ranking**: After ANN retrieval, a cross-encoder model re-ranks results for higher precision (Cohere Rerank, BGE-reranker)

### Use Cases
1. **Semantic Search**: Find documents by meaning, not keywords. "Query: car crash" finds articles about "vehicular accidents"
2. **Retrieval-Augmented Generation (RAG)**: LLM retrieves relevant context before answering — turns a static model into a dynamic knowledge system
3. **Recommendation Systems**: Find items similar to what a user previously liked
4. **Anomaly Detection**: Identify vectors far from all cluster centroids
5. **Duplicate Detection / Deduplication**: Find near-identical documents even if phrased differently
6. **Multi-modal Search**: Query by image to find similar images; query by text to find matching images (CLIP model)

### Limitations and Gotchas
- **Embedding models have context windows**: most cap at 512 or 8,192 tokens — long documents must be chunked
- **Domain shift**: A general-purpose embedding model may perform poorly on specialized domains (medical, legal, code) — fine-tuning or domain-specific models may be needed
- **Index build time**: HNSW indexes are fast to query but slow to build and memory-intensive (roughly 1–2 bytes/dimension/vector)
- **Stale embeddings**: If the underlying model changes, all stored embeddings must be recomputed

## Related
- [[retrieval-augmented-generation]]
- [[transformer-architecture]]
- [[machine-learning-fundamentals]]
