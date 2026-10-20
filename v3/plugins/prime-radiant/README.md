---
title: "Prime Radiant Plugin"
tags: ["mathematics", "coherence", "consensus", "hallucination", "stability", "spectral", "causal", "topology", "MCP"]
category: "plugins/prime-radiant"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/plugin-prime-radiant

**Mathematical AI that catches contradictions, verifies consensus, and prevents hallucinations before they cause problems.**

---

## 💡 概述

This plugin brings advanced mathematical techniques to Claude Flow for ensuring AI reliability:

- **Coherence Checking** - Detect when information contradicts itself before storing it
- **Consensus Verification** - Mathematically verify that multiple agents actually agree
- **Hallucination Prevention** - Catch inconsistent RAG results before they reach users
- **Stability Analysis** - Monitor swarm health using spectral graph theory
- **Causal Inference** - Understand cause-and-effect, not just correlations

Think of it as a mathematical "sanity check" layer that catches logical inconsistencies that traditional validation misses.

---

## ⚡ 功能

### 6 Mathematical Engines

| Engine | What It Does | Use Case |
|--------|--------------|----------|
| **Cohomology** | Measures contradiction using Sheaf Laplacian | Memory validation, fact-checking |
| **Spectral** | Analyzes stability via eigenvalues | Swarm health, network topology |
| **Causal** | Do-calculus for cause-effect reasoning | Root cause analysis, optimization |
| **Quantum** | Persistent homology for structure | Clustering, pattern discovery |
| **Category** | Morphism and functor operations | Schema transformations |
| **HoTT** | Homotopy Type Theory proofs | Formal verification |

### Hooks (Automatic Integration)

| Hook | When It Runs | What It Does |
|------|--------------|--------------|
| `pr/pre-memory-store` | Before memory storage | Blocks contradictory entries |
| `pr/pre-consensus` | Before consensus voting | Validates proposal consistency |
| `pr/post-swarm-task` | After swarm tasks | Analyzes stability metrics |
| `pr/pre-rag-retrieval` | Before RAG results | Filters inconsistent documents |

### Practical Examples

#### Basic: Check if Information is Consistent

```typescript
const result = await mcp.call('pr_coherence_check', {
  vectors: [
    embedding("The project deadline is Friday"),
    embedding("We have two more weeks"),
    embedding("The deadline was moved to next month")
  ],
  threshold: 0.3
});

// Result
{
  coherent: false,
  energy: 0.72,  // High energy = contradiction
  violations: ["Statement 3 contradicts statements 1-2"],
  confidence: 0.28
}
```

**Energy levels explained:**
- `0.0-0.1` = Fully consistent, safe to store
- `0.1-0.3` = Minor inconsistencies, warning zone
- `0.3-0.7` = Significant contradictions, needs review
- `0.7-1.0` = Major contradictions, reject

#### Basic: Verify Multi-Agent Consensus

```typescript
const consensus = await mcp.call('pr_consensus_verify', {
  agentStates: [
    { agentId: 'researcher', embedding: [...], vote: true },
    { agentId: 'analyst', embedding: [...], vote: true },
    { agentId: 'reviewer', embedding: [...], vote: false }
  ],
  consensusThreshold: 0.8
});
```

#### Intermediate: Analyze Swarm Stability

```typescript
const stability = await mcp.call('pr_spectral_analyze', {
  adjacencyMatrix: [
    [0, 1, 1, 0, 0],
    [1, 0, 1, 1, 0],
    [1, 1, 0, 1, 1],
    [0, 1, 1, 0, 1],
    [0, 0, 1, 1, 0]
  ],
  analyzeType: 'stability'
});
```

**What to watch for:**
- `spectralGap < 0.1` = Unstable, agents may desynchronize
- `stabilityIndex < 0.5` = Warning, coordination issues likely

#### Intermediate: Causal Inference

```typescript
const causal = await mcp.call('pr_causal_infer', {
  treatment: 'agent_count',
  outcome: 'task_completion_time',
  graph: {
    nodes: ['agent_count', 'coordination_overhead', 'task_completion_time', 'task_complexity'],
    edges: [
      ['agent_count', 'task_completion_time'],
      ['agent_count', 'coordination_overhead'],
      ['coordination_overhead', 'task_completion_time'],
      ['task_complexity', 'agent_count'],
      ['task_complexity', 'task_completion_time']
    ]
  }
});
```

#### Advanced: Memory Gate (Auto-Reject Contradictions)

```typescript
const result = await mcp.call('pr_memory_gate', {
  entry: {
    key: 'project-status',
    content: 'Project is on track for Friday deadline',
    embedding: embedding("Project is on track for Friday deadline")
  },
  contextEmbeddings: [
    embedding("Deadline extended to next month"),
    embedding("Team requested more time")
  ],
  thresholds: {
    warn: 0.3,
    reject: 0.7
  }
});
```

#### Advanced: Prevent RAG Hallucinations

```typescript
const context = await rag.retrieve('What is the project deadline?');
// If docs were contradictory:
{
  documents: [...],  // Filtered to consistent subset
  coherenceFiltered: true,
  originalCount: 5,
  filteredCount: 3,
  removedForCoherence: ['doc-4', 'doc-5'],
  originalCoherenceEnergy: 0.68
}
```

#### Expert: Quantum Topology Analysis

```typescript
const topology = await mcp.call('pr_quantum_topology', {
  points: embeddings,
  maxDimension: 2
});
// bettiNumbers: { b0: 3 (connected components), b1: 1 (loop), b2: 0 }
```

#### Exotic: Real-Time Swarm Health Dashboard

```typescript
async function monitorSwarmHealth() {
  const adjacency = await getSwarmAdjacencyMatrix();
  const health = await mcp.call('pr_spectral_analyze', {
    adjacencyMatrix: adjacency,
    analyzeType: 'stability'
  });
  if (!health.stable) {
    console.warn('?? Swarm instability detected!');
    await swarm.rebalance();
  }
}
setInterval(monitorSwarmHealth, 30000);
```

---

## 📊 指标

### Performance

| Operation | Latency | Notes |
|-----------|---------|-------|
| Coherence check | <5ms | Per validation |
| Spectral analysis | <20ms | Up to 100x100 matrix |
| Causal inference | <10ms | Per query |
| Quantum topology | <50ms | Per computation |
| Memory overhead | <10MB | Including WASM |

---

## 🏛️ 设计

### Configuration

```yaml
# claude-flow.config.yaml
plugins:
  prime-radiant:
    enabled: true
    config:
      coherence:
        warnThreshold: 0.3
        rejectThreshold: 0.7
        cacheEnabled: true
      spectral:
        stabilityThreshold: 0.1
        maxMatrixSize: 1000
      causal:
        maxBackdoorPaths: 10
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

「待补充」

---

## 🚀 快速开始

### Installation

```bash
npm install @claude-flow/plugin-prime-radiant
```

---

## ⚙️ 技术栈

「待补充」

## License

MIT