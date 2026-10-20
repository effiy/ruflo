---
title: "RuVector NPM Package API Documentation"
tags: ["ruvector", "npm", "api", "vector-database", "mcp", "hooks", "ast-analysis", "diff-analysis"]
category: "@claude-flow/cli/src/ruvector"
created: "2024-01-01"
updated: "2026-10-20"
source: "internal"
type: "index"
status: "stable"
lifecycle: "active"
---

# RuVector NPM Package API Documentation

Version: 0.1.95
Package: `ruvector`
Repository: https://github.com/ruvnet/ruvector

---

## 💡 概述

RuVector is a high-performance vector database for Node.js with automatic native/WASM fallback. It provides self-learning intelligence for Claude Code with Q-learning optimization, vector memory, and automatic agent routing.

---

## ⚡ 功能

### MCP Tools Overview

#### Core Routing Functions
- `hooks_route` - Routes a task to the best agent based on learned patterns
- `hooks_route_enhanced` - Enhanced routing using AST complexity, coverage, and diff analysis signals

#### AST Analysis Functions
- `hooks_ast_analyze` - Parses file AST and extracts symbols, imports, and complexity metrics
- `hooks_ast_complexity` - Calculates cyclomatic and cognitive complexity metrics for files

#### Diff Analysis Functions
- `hooks_diff_analyze` - Analyzes git diff with semantic embeddings and risk scoring
- `hooks_diff_classify` - Classifies change type based on diff patterns

#### Coverage Analysis Functions
- `hooks_coverage_route` - Gets coverage-aware agent routing for a file
- `hooks_coverage_suggest` - Suggests tests for files based on coverage data

#### Graph Analysis Functions
- `hooks_graph_mincut` - Finds optimal code boundaries using MinCut algorithm (Stoer-Wagner)
- `hooks_graph_cluster` - Detects code communities using spectral or Louvain clustering

#### Additional MCP Tools

**Memory Functions:** `hooks_remember`, `hooks_recall`

**Learning Functions:** `hooks_learn`, `hooks_batch_learn`, `hooks_learning_config`, `hooks_learning_stats`

**Trajectory Functions:** `hooks_trajectory_begin`, `hooks_trajectory_step`, `hooks_trajectory_end`

**Security Functions:** `hooks_security_scan`

**Neural Functions:** `hooks_attention_info`, `hooks_gnn_info`, `hooks_rag_context`

---

## 📊 指标

### Performance Characteristics

| Operation | Latency | Notes |
|-----------|---------|-------|
| ONNX inference | ~400ms | Initial embedding |
| HNSW search | ~0.045ms | 8,800x faster than inference |
| Memory cache | ~0.01ms | 40,000x speedup |
| Native Rust | <0.5ms | p50 latency |
| WASM fallback | 10-50ms | Universal compatibility |

---

## 🏛️ 设计

### Core Routing: `hooks_route`

Routes a task to the best agent based on learned patterns.

**Input Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `task` | string | Yes | Task description to route |
| `file` | string | No | File path for context-aware routing |

**Return Type:**
```typescript
interface RouteResult {
  success: boolean;
  task: string;
  file?: string;
  agent: string;           // Recommended agent (e.g., "typescript-developer")
  confidence: number;      // Confidence score 0.0-1.0
  reason: string;          // Explanation for routing decision
  alternates?: string[];   // Alternative agent suggestions
  sonaPatterns?: number;   // Number of SONA patterns used (if engine enabled)
  engineRouted?: boolean;  // Whether full engine was used
}
```

### Enhanced Routing: `hooks_route_enhanced`

Enhanced routing using AST complexity, coverage, and diff analysis signals.

**Return Type:**
```typescript
interface EnhancedRouteResult {
  success: boolean;
  agent: string;
  confidence: number;
  signals: {
    complexity?: number;    // Cyclomatic complexity
    coverage?: number;      // Test coverage percentage
    riskScore?: number;     // Change risk assessment
    diffCategory?: string;  // feature/bugfix/refactor
  };
  explanation: string;
}
```

### AST Analysis: `hooks_ast_analyze`

**Return Type:**
```typescript
interface FileAnalysis {
  file: string;
  language: string;           // TypeScript, JavaScript, Python, etc.
  imports: ImportInfo[];      // Import statements
  exports: ExportInfo[];      // Export statements
  functions: FunctionInfo[];  // Function definitions
  classes: ClassInfo[];       // Class definitions
  variables: string[];        // Variable declarations
  types: string[];           // Type definitions
  complexity: number;        // Overall complexity score
  lines: number;             // Total lines
  parseTime: number;         // Parse duration in ms
}

interface FunctionInfo {
  name: string;
  params: string[];
  returnType?: string;
  async: boolean;
  exported: boolean;
  startLine: number;
  endLine: number;
  complexity: number;
  calls: string[];           // Functions called within
}

interface ClassInfo {
  name: string;
  extends?: string;
  implements: string[];
  methods: FunctionInfo[];
  properties: string[];
  exported: boolean;
  startLine: number;
  endLine: number;
}

interface ImportInfo {
  source: string;
  default?: string;
  named: string[];
  namespace?: string;
  type: 'esm' | 'commonjs' | 'dynamic';
}
```

### Diff Analysis: `hooks_diff_analyze`

**Return Type:**
```typescript
interface CommitAnalysis {
  hash: string;
  message: string;
  author: string;
  date: string;
  files: DiffAnalysis[];
  totalAdditions: number;
  totalDeletions: number;
  riskScore: number;        // 0.0-1.0 risk assessment
  embedding?: number[];     // Semantic embedding
}

interface DiffAnalysis {
  file: string;
  hunks: DiffHunk[];
  totalAdditions: number;
  totalDeletions: number;
  complexity: number;
  riskScore: number;
  category: 'feature' | 'bugfix' | 'refactor' | 'docs' | 'test' | 'config' | 'unknown';
  embedding?: number[];
}

interface DiffHunk {
  file: string;
  oldStart: number;
  oldLines: number;
  newStart: number;
  newLines: number;
  content: string;
  additions: string[];
  deletions: string[];
}
```

### Programmatic API

```typescript
// VectorDB
import { VectorDb } from 'ruvector';

const db = new VectorDb({
  dimensions: 384,           // Must match embedding model
  maxElements: 10000,
  storagePath: './vectors.db'
});

// Insert
await db.insert({
  id: 'doc1',
  vector: new Float32Array(384),
  metadata: { title: 'Document 1' }
});

// Search
const results = await db.search({
  vector: queryVector,
  k: 5,
  threshold: 0.7
});

// Get / Delete
const doc = await db.get('doc1');
await db.delete('doc1');

// Core Modules
import { getCodeParser } from 'ruvector/dist/core/ast-parser';
import diffEmbeddings from 'ruvector/dist/core/diff-embeddings';
import coverage from 'ruvector/dist/core/coverage-router';
import graph from 'ruvector/dist/core/graph-algorithms';
```

### Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `RUVECTOR_INTELLIGENCE_ENABLED` | `true` | Enable/disable intelligence |
| `RUVECTOR_LEARNING_RATE` | `0.1` | Q-learning rate (0.0-1.0) |
| `RUVECTOR_MEMORY_BACKEND` | `rvlite` | Memory storage backend |
| `INTELLIGENCE_MODE` | `treatment` | A/B testing mode |

---

## 📁 目录

「待补充」

---

## 🔗 资源

- [npm Package](https://www.npmjs.com/package/ruvector)
- [GitHub Repository](https://github.com/ruvnet/ruvector)
- [Hooks Documentation](https://github.com/ruvnet/ruvector/blob/main/npm/packages/ruvector/HOOKS.md)

---

## 🚀 快速开始

### Installation

```bash
npm install ruvector
```

### MCP Server Integration

Add to Claude Code:

```bash
claude mcp add ruvector-mcp -- npx ruvector mcp-server
```

### CLI Commands

```bash
# Initialize hooks
npx ruvector hooks init --pretrain --build-agents quality

# Verify setup
npx ruvector hooks verify
npx ruvector hooks doctor --fix

# Analysis
npx ruvector hooks ast-analyze <file> --json
npx ruvector hooks ast-complexity <files> --threshold 10
npx ruvector hooks diff-analyze [commit] --json
npx ruvector hooks diff-classify [commit]
npx ruvector hooks coverage-route <file>
npx ruvector hooks coverage-suggest <files>
npx ruvector hooks graph-mincut <files>
npx ruvector hooks graph-cluster <files> --method louvain

# Memory
npx ruvector hooks remember "context" -t project
npx ruvector hooks recall "query"
npx ruvector hooks route "task description"

# Stats and export
npx ruvector hooks stats
npx ruvector hooks export -o backup.json
npx ruvector hooks import backup.json --merge
```

---

## ⚙️ 技术栈

### Dependencies

- `@modelcontextprotocol/sdk`: ^1.0.0
- `@ruvector/attention`: ^0.1.3
- `@ruvector/core`: ^0.1.25
- `@ruvector/gnn`: ^0.1.22
- `@ruvector/sona`: ^0.1.4
- `@xenova/transformers`: ^2.17.2