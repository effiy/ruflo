---
title: "@claude-flow/embeddings"
tags: ["embeddings", "vector", "onnx", "openai", "transformers", "hyperbolic", "hierarchical", "nlp", "typescript", "npm"]
category: "v3/@claude-flow/embeddings"
created: "2024-01-01"
updated: "2026-10-20"
source: "internal"
type: "index"
status: "stable"
lifecycle: "active"
---

## 💡 概述

[![npm version](https://img.shields.io/npm/v/@claude-flow/embeddings.svg)](https://www.npmjs.com/package/@claude-flow/embeddings)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/embeddings.svg)](https://www.npmjs.com/package/@claude-flow/embeddings)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-blue.svg)](https://www.typescriptlang.org/)
[![Performance](https://img.shields.io/badge/Performance-<5ms-brightgreen.svg)](https://github.com/ruvnet/claude-flow)

> High-performance embedding generation module for Claude Flow V3 — multi-provider support with persistent caching, document chunking, normalization, hyperbolic embeddings, and neural substrate integration.

## Standalone use (without the Ruflo CLI)

This package runs on its own — no `@claude-flow/cli`, no MCP server, no API key required for the recipes below (the `MockEmbeddingService` gives deterministic 384-dim vectors so examples are reproducible). Swap to `OpenAIEmbeddingService` / `TransformersEmbeddingService` / `AgenticFlowEmbeddingService` when you're ready for real embeddings.

---

## ⚡ 功能

### Core Embedding

- **Multiple Providers** — Agentic-Flow (ONNX), OpenAI, Transformers.js, and Mock
- **Auto-Install** — Automatically installs agentic-flow when using `provider: 'auto'`
- **Smart Fallback** — Graceful fallback chain: agentic-flow → transformers → mock
- **LRU + Disk Caching** — In-memory LRU + SQLite persistent cache with TTL
- **Batch Processing** — Efficient batch embedding with partial cache hits
- **Similarity Functions** — Cosine, Euclidean, and dot product metrics
- **75x Faster** — Agentic-flow ONNX is 75x faster than Transformers.js

### Advanced Features

- **Document Chunking** — Character, sentence, paragraph, and token-based chunking with overlap
- **Multiple Normalization** — L2, L1, min-max, and z-score normalization
- **Hyperbolic Embeddings** — Poincaré ball model for hierarchical representations
- **Neural Substrate** — Semantic drift detection, memory physics, swarm coordination
- **Persistent Cache** — SQLite-backed disk cache with LRU eviction and TTL

### Provider Comparison

| Provider | Latency | Quality | Cost | Offline |
|----------|---------|---------|------|---------|
| **Agentic-Flow** | ~3ms | Good | Free | Yes |
| **OpenAI** | ~50-100ms | Excellent | $0.02-0.13/1M tokens | No |
| **Transformers.js** | ~230ms | Good | Free | Yes |
| **Mock** | <1ms | N/A | Free | Yes |

### Agentic-Flow (Recommended)

| Model | Dimensions | Speed | Best For |
|-------|------------|-------|----------|
| `default` | 384 | 3ms | General purpose, fastest |

### OpenAI Models

| Model | Dimensions | Max Tokens | Best For |
|-------|------------|------------|----------|
| `text-embedding-3-small` | 1536 | 8191 | General purpose, cost-effective |
| `text-embedding-3-large` | 3072 | 8191 | Highest quality |
| `text-embedding-ada-002` | 1536 | 8191 | Legacy support |

### Transformers.js Models

| Model | Dimensions | Size | Best For |
|-------|------------|------|----------|
| `Xenova/all-MiniLM-L6-v2` | 384 | 23MB | Fast, general purpose |
| `Xenova/all-mpnet-base-v2` | 768 | 110MB | Higher quality |
| `Xenova/bge-small-en-v1.5` | 384 | 33MB | Retrieval optimized |

### Document Chunking

Split long documents into overlapping chunks for embedding:

```typescript
import { chunkText, estimateTokens, reconstructFromChunks } from '@claude-flow/embeddings';

// Chunk by sentence (default)
const result = chunkText(longDocument, {
  maxChunkSize: 512,
  overlap: 50,
  strategy: 'sentence',  // 'character' | 'sentence' | 'paragraph' | 'token'
  minChunkSize: 100,
});

console.log('Chunks:', result.totalChunks);
result.chunks.forEach((chunk, i) => {
  console.log(`Chunk ${i}: ${chunk.length} chars, ~${chunk.tokenCount} tokens`);
});
```

### Normalization

```typescript
import {
  l2Normalize,    // Unit vector (Euclidean norm = 1)
  l1Normalize,    // Manhattan norm = 1
  minMaxNormalize, // Values in [0, 1]
  zScoreNormalize, // Mean 0, std 1
  normalize,       // Generic with type option
  l2Norm,
  isNormalized,
} from '@claude-flow/embeddings';
```

### Hyperbolic Embeddings (Poincare Ball)

Transform embeddings to hyperbolic space for better hierarchical representation:

```typescript
import {
  euclideanToPoincare,
  poincareToEuclidean,
  hyperbolicDistance,
  mobiusAdd,
  isInPoincareBall,
  batchEuclideanToPoincare,
  hyperbolicCentroid,
} from '@claude-flow/embeddings';

const euclidean = new Float32Array([0.5, 0.3, 0.2]);
const poincare = euclideanToPoincare(euclidean);

console.log(isInPoincareBall(poincare));  // true

// Round-trip conversion
const back = poincareToEuclidean(poincare);

// Hyperbolic distance (geodesic in Poincaré ball)
const a = euclideanToPoincare(new Float32Array([0.1, 0.2, 0.1]));
const b = euclideanToPoincare(new Float32Array([0.3, 0.1, 0.2]));
const dist = hyperbolicDistance(a, b);
```

**Why Hyperbolic?** Hyperbolic space has natural properties for representing hierarchical data:
- **Exponential growth** — Tree-like structures fit naturally
- **Better hierarchy** — Parent-child relationships preserved
- **Lower distortion** — Taxonomies represented with less error

### Neural Substrate Integration

```typescript
import {
  NeuralEmbeddingService,
  createNeuralService,
  isNeuralAvailable,
  listEmbeddingModels,
  downloadEmbeddingModel,
} from '@claude-flow/embeddings';

const neural = createNeuralService({ dimension: 384 });
await neural.init();

if (neural.isAvailable()) {
  // Semantic drift detection
  await neural.setDriftBaseline('Initial context about the topic');
  const drift = await neural.detectDrift('New input to check for drift');
  
  // Memory with interference detection
  const stored = await neural.storeMemory('mem-1', 'Important information');
  
  // Recall by similarity
  const memories = await neural.recallMemories('query', 5);
  
  // Swarm coordination
  await neural.addSwarmAgent('agent-1', 'researcher');
  const coordination = await neural.coordinateSwarm('Analyze this task');
  
  // Coherence checking
  await neural.calibrateCoherence(['good output 1', 'good output 2']);
  const coherence = await neural.checkCoherence('Output to check');
}
```

### Persistent Disk Cache

SQLite-backed persistent cache for embeddings:

```typescript
import { PersistentEmbeddingCache, isPersistentCacheAvailable } from '@claude-flow/embeddings';

const cache = new PersistentEmbeddingCache({
  dbPath: './embeddings.db',
  maxSize: 10000,
  ttlMs: 7 * 24 * 60 * 60 * 1000,  // 7 day TTL
});

await cache.init();
await cache.set('my text', new Float32Array([0.1, 0.2, 0.3]));
const embedding = await cache.get('my text');
const stats = await cache.getStats();
await cache.close();
```

```typescript
// Enable in Embedding Service
const service = createEmbeddingService({
  provider: 'openai',
  apiKey: process.env.OPENAI_API_KEY!,
  persistentCache: {
    enabled: true,
    dbPath: './cache/embeddings.db',
    maxSize: 50000,
    ttlMs: 30 * 24 * 60 * 60 * 1000,  // 30 days
  },
  normalization: 'l2',
});
```

### Batch Processing

```typescript
const result = await service.embedBatch([
  'Document 1: Introduction to machine learning',
  'Document 2: Deep learning fundamentals',
  'Document 3: Natural language processing',
  'Document 4: Computer vision basics',
]);

console.log('Batch Results:', {
  count: result.embeddings.length,
  totalLatency: `${result.totalLatencyMs}ms`,
  avgLatency: `${result.avgLatencyMs}ms`,
  cacheHits: result.cacheStats?.hits,
  cacheMisses: result.cacheStats?.misses,
  tokensUsed: result.usage?.totalTokens,
});
```

### Similarity Functions

```typescript
import {
  cosineSimilarity,
  euclideanDistance,
  dotProduct,
  computeSimilarity,
} from '@claude-flow/embeddings';

// Cosine similarity (most common for embeddings)
const cosine = cosineSimilarity(embedding1, embedding2);  // 0.0 to 1.0

// Euclidean distance
const distance = euclideanDistance(embedding1, embedding2);  // 0.0 to infinity

// Dot product
const dot = dotProduct(embedding1, embedding2);

// Generic similarity with metric selection
const result = computeSimilarity(embedding1, embedding2, 'cosine');
```

### Cache Management

```typescript
const stats = service.getCacheStats();
service.clearCache();
await service.shutdown();
```

### Event System

```typescript
service.addEventListener((event) => {
  switch (event.type) {
    case 'embed_start': break;
    case 'embed_complete': break;
    case 'embed_error': break;
    case 'cache_hit': break;
    case 'batch_start': break;
    case 'batch_complete': break;
    case 'cache_eviction': break;
  }
});

service.removeEventListener(listener);
```

---

## 📊 指标

「待补充」

---

## 🏛️ 设计

### Factory Functions

```typescript
import {
  createEmbeddingService,
  createEmbeddingServiceAsync,
  getEmbedding
} from '@claude-flow/embeddings';

// Sync: Create with known provider
const service = createEmbeddingService({
  provider: 'openai',
  apiKey: 'your-api-key',
  model: 'text-embedding-3-small',
});

// Async: Auto-select best provider with fallback
const autoService = await createEmbeddingServiceAsync({
  provider: 'auto',       // agentic-flow → transformers → mock
  autoInstall: true,
  fallback: 'transformers',
});

// Quick one-off embedding
const embedding = await getEmbedding('Hello world', {
  provider: 'mock',
  dimensions: 384,
});
```

### OpenAI Provider

```typescript
import { OpenAIEmbeddingService } from '@claude-flow/embeddings';

const service = new OpenAIEmbeddingService({
  provider: 'openai',
  apiKey: process.env.OPENAI_API_KEY!,
  model: 'text-embedding-3-small',
  dimensions: 1536,
  baseURL: 'https://api.openai.com/v1/embeddings',
  timeout: 30000,
  maxRetries: 3,
  cacheSize: 1000,
});
```

### Agentic-Flow Provider (Fastest)

```typescript
import { AgenticFlowEmbeddingService } from '@claude-flow/embeddings';

const service = new AgenticFlowEmbeddingService({
  provider: 'agentic-flow',
  modelId: 'default',
  cacheSize: 256,
});

// 75x faster than Transformers.js (3ms vs 233ms)
const result = await service.embed('Your text here');
```

### Transformers.js Provider (Local)

```typescript
import { TransformersEmbeddingService } from '@claude-flow/embeddings';

const service = new TransformersEmbeddingService({
  provider: 'transformers',
  model: 'Xenova/all-MiniLM-L6-v2',
  cacheSize: 1000,
});
```

### Mock Provider (Testing)

```typescript
import { MockEmbeddingService } from '@claude-flow/embeddings';

const service = new MockEmbeddingService({
  provider: 'mock',
  dimensions: 384,
  simulatedLatency: 10,
  cacheSize: 100,
});
```

### Integration with Memory Module

```typescript
import { createEmbeddingService } from '@claude-flow/embeddings';
import { HNSWIndex } from '@claude-flow/memory';

const embeddings = createEmbeddingService({
  provider: 'openai',
  apiKey: process.env.OPENAI_API_KEY!,
  model: 'text-embedding-3-small',
});

const index = new HNSWIndex({
  dimensions: 1536,
  metric: 'cosine',
});

const documents = ['Doc 1 content', 'Doc 2 content', 'Doc 3 content'];
const { embeddings: vectors } = await embeddings.embedBatch(documents);

vectors.forEach((vector, i) => {
  index.addPoint(`doc-${i}`, new Float32Array(vector));
});

const queryResult = await embeddings.embed('Search query');
const results = await index.search(new Float32Array(queryResult.embedding), 5);
```

### Error Handling

```typescript
try {
  const result = await service.embed('Your text');
} catch (error) {
  if (error instanceof Error) {
    if (error.message.includes('API error')) {
      // Handle API errors (rate limits, auth, etc.)
    } else if (error.message.includes('Failed to initialize')) {
      // Handle model loading errors (Transformers.js)
    }
  }
}
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Related Packages

- [@claude-flow/memory](../memory) — HNSW indexing and vector storage
- [@claude-flow/providers](../providers) — Multi-LLM provider system
- [@claude-flow/neural](../neural) — SONA learning integration

### License

MIT

---

## 🚀 快速开始

### Recipe — Embed + compare without a network call

```typescript
import {
  MockEmbeddingService,
  cosineSimilarity,
} from '@claude-flow/embeddings';

const service = new MockEmbeddingService({ dimension: 384 });

const [{ embedding: cat }, { embedding: dog }, { embedding: car }] =
  await Promise.all([
    service.embed('a friendly cat'),
    service.embed('a friendly dog'),
    service.embed('a red sports car'),
  ]);

console.log('cat ↔ dog:', cosineSimilarity(cat, dog).toFixed(4));
console.log('cat ↔ car:', cosineSimilarity(car, dog).toFixed(4));
```

### Provider swap — same surface, real vectors

```typescript
import { createEmbeddingService, cosineSimilarity } from '@claude-flow/embeddings';

// OpenAI
const openai = createEmbeddingService({
  provider: 'openai',
  model: 'text-embedding-3-small',
  apiKey: process.env.OPENAI_API_KEY!,
});

// Or local ONNX via @huggingface/transformers (no API key needed)
const local = createEmbeddingService({
  provider: 'transformers',
  model: 'Xenova/all-MiniLM-L6-v2', // 384-dim, ~25MB
});

await openai.initialize();
const v = await openai.embed('hello world');
```

### Standard Quick Start

```typescript
import { createEmbeddingService, cosineSimilarity } from '@claude-flow/embeddings';

const service = createEmbeddingService({
  provider: 'openai',
  apiKey: process.env.OPENAI_API_KEY!,
  model: 'text-embedding-3-small',
  dimensions: 1536,
});

const result = await service.embed('Hello, world!');
console.log(`Embedding dimension: ${result.embedding.length}`);
console.log(`Latency: ${result.latencyMs}ms`);

const batchResult = await service.embedBatch([
  'First document',
  'Second document',
  'Third document',
]);

const similarity = cosineSimilarity(
  batchResult.embeddings[0],
  batchResult.embeddings[1]
);
```

### CLI Usage

```bash
# Generate embedding from CLI
claude-flow embeddings embed "Your text here"

# Batch embed from file
claude-flow embeddings batch documents.txt -o embeddings.json

# Similarity search
claude-flow embeddings search "query" --index ./vectors

# Initialize agentic-flow model
claude-flow embeddings init --provider agentic-flow

# Document chunking
claude-flow embeddings chunk document.txt --strategy sentence --max-size 512

# Normalize embedding file
claude-flow embeddings normalize embeddings.json --type l2 -o normalized.json

# Convert to hyperbolic
claude-flow embeddings hyperbolic embeddings.json -o poincare.json

# Neural operations
claude-flow embeddings neural drift --baseline "context" --input "check this"
claude-flow embeddings neural store --id mem-1 --content "data"
claude-flow embeddings neural recall "query" --top-k 5

# List/download models
claude-flow embeddings models list
claude-flow embeddings models download all-MiniLM-L6-v2

# Cache management
claude-flow embeddings cache stats
claude-flow embeddings cache clear --older-than 7d
```

### Installation

```bash
npm install @claude-flow/embeddings
```

---

## ⚙️ 技术栈

- TypeScript 5.0+
- Agentic-Flow ONNX (75x faster than Transformers.js)
- OpenAI embedding API
- @huggingface/transformers (WASM)
- SQLite (via sql.js) for persistent caching
- Poincaré ball model for hyperbolic embeddings
- L2/L1/min-max/z-score normalization