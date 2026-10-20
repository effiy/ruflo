---
title: "@claude-flow/plugin-hyperbolic-reasoning"
tags: ["hyperbolic", "reasoning", "poincare-ball", "embeddings", "hierarchical", "gnn"]
category: "plugins/hyperbolic-reasoning"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/plugin-hyperbolic-reasoning

[![npm version](https://img.shields.io/npm/v/@claude-flow/plugin-hyperbolic-reasoning.svg)](https://www.npmjs.com/package/@claude-flow/plugin-hyperbolic-reasoning)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/plugin-hyperbolic-reasoning.svg)](https://www.npmjs.com/package/@claude-flow/plugin-hyperbolic-reasoning)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 💡 概述

Hyperbolic space reasoning plugin for Claude Flow V3 - Poincare ball embeddings for hierarchical data, hyperbolic GNN reasoning, and attention over non-Euclidean spaces.

---

## ⚡ 功能

- **Poincare Ball Embeddings** - Hyperbolic space representations for hierarchical data
- **Hyperbolic Distance Metrics** - Distance computation in curved spaces
- **GNN Reasoning** - Graph Neural Networks in hyperbolic space
- **Attention Mechanisms** - Flash Attention optimized for hyperbolic geometry
- **Hierarchical Clustering** - Natural hierarchical clustering in hyperbolic space
- **Embedding Space Transformations** - Euclidean to hyperbolic and back
- **Visualization** - 2D Poincare disk projections

### WASM Acceleration

| Component | WASM Package | Speed Improvement |
|-----------|-------------|-------------------|
| Hyperbolic HNSW | `ruvector-hyperbolic-hnsw-wasm` | Poincare ball model |
| Flash Attention | `ruvector-attention-wasm` | 2.49x-7.47x speedup |

---

## 📊 指标

| Metric | Target |
|--------|--------|
| Embedding dimensionality | 2-200 |
| Poincare ball curvature | -1 (default) |
| Distance computation | <0.1ms |
| Hierarchical clustering depth | Configurable |

---

## 🏛️ 设计

### Poincare Ball Embeddings

```typescript
import { HyperbolicEmbedder } from '@claude-flow/plugin-hyperbolic-reasoning';

const embedder = new HyperbolicEmbedder({
  dimensions: 64,
  curvature: -1,  // Poincare ball curvature
  learningRate: 0.1
});

// Embed hierarchical data
const treeData = {
  id: 'root',
  children: [
    { id: 'child-1', children: [{ id: 'grandchild-1' }] },
    { id: 'child-2' }
  ]
};

const embeddings = await embedder.embed(treeData);
console.log(Object.keys(embeddings)); // ['root', 'child-1', 'child-2', 'grandchild-1']

// Distance between nodes
const dist = embedder.distance(embeddings['root'], embeddings['grandchild-1']);
```

### Hyperbolic Distance

```typescript
import { HyperbolicDistance } from '@claude-flow/plugin-hyperbolic-reasoning';

const distance = new HyperbolicDistance({ curvature: -1 });

// Poincare distance
const d = distance.poincare(vector1, vector2);

// Geodesic distance
const g = distance.geodesic(point1, point2);

// Distance matrix
const matrix = distance.pairwise(vectors);
```

### GNN Reasoning

```typescript
import { HyperbolicGNN } from '@claude-flow/plugin-hyperbolic-reasoning';

const gnn = new HyperbolicGNN({
  layers: 3,
  hiddenDim: 128,
  curvature: -1,
  attention: true
});

const nodeEmbeddings = await gnn.encode(graph);
const predictions = await gnn.predict(nodeEmbeddings);
```

### Attention over Hyperbolic Space

```typescript
import { HyperbolicAttention } from '@claude-flow/plugin-hyperbolic-reasoning';

const attention = new HyperbolicAttention({
  mechanism: 'flash',  // 'flash' | 'standard' | 'linear'
  numHeads: 8,
  curvature: -1
});

const result = await attention.compute({
  query: queryEmbedding,
  keys: keyEmbeddings,
  values: valueEmbeddings
});
```

### Hierarchical Clustering

```typescript
import { HyperbolicClusterer } from '@claude-flow/plugin-hyperbolic-reasoning';

const clusterer = new HyperbolicClusterer({
  curvature: -1,
  minClusterSize: 5,
  maxDepth: 10
});

const clusters = await clusterer.cluster(dataPoints);

for (const cluster of clusters) {
  console.log(`Cluster at depth ${cluster.depth}: ${cluster.members.length} items`);
}
```

### Space Transformations

```typescript
import { SpaceTransformer } from '@claude-flow/plugin-hyperbolic-reasoning';

const transformer = new SpaceTransformer({ curvature: -1 });

// Euclidean -> Hyperbolic (exponential map)
const hyperbolicVec = await transformer.toHyperbolic(euclideanVector);

// Hyperbolic -> Euclidean (logarithmic map)
const euclideanVec = await transformer.toEuclidean(hyperbolicVector);

// Mobius addition
const sum = await transformer.mobiusAdd(a, b);
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Installation

```bash
npx claude-flow@v3alpha plugins install @claude-flow/plugin-hyperbolic-reasoning
```

### Mathematical Foundation

This plugin is built on the Poincare ball model of hyperbolic geometry:

- **Distance**: d(u, v) = arcosh(1 + 2 * ||u - v||^2 / ((1 - ||u||^2)(1 - ||v||^2)))
- **Exponential map**: exp_x(v) = x ⊕ (tanh(||v|| / (1 - ||x||^2)) * v / ||v||)
- **Logarithmic map**: log_x(y) = (1 - ||x||^2) * artanh(||-x ⊕ y||) * (-x ⊕ y) / ||-x ⊕ y||

### Related Packages

- [@claude-flow/embeddings](../../@claude-flow/embeddings) - Embedding system
- [ruvector-upstream](../ruvector-upstream) - WASM acceleration dependencies

### License

MIT

---

## 🚀 快速开始

```typescript
import { HyperbolicReasoningPlugin } from '@claude-flow/plugin-hyperbolic-reasoning';

const plugin = new HyperbolicReasoningPlugin();
await plugin.initialize();

// Embed hierarchical data
const data = { id: 'root', children: [...] };
const embeddings = await plugin.embed(data);

// Find similar items
const similar = await plugin.search(queryVector, { limit: 10 });

// Cluster hierarchically
const clusters = await plugin.cluster(dataPoints);
```

---

## ⚙️ 技术栈

- TypeScript
- Node.js
- Poincare ball geometry
- RuVector WASM (Hyperbolic HNSW, Flash Attention)