---
title: "@claude-flow/plugin-code-intelligence"
tags: ["code-intelligence", "code-analysis", "ast", "complexity", "dependencies", "refactoring"]
category: "plugins/code-intelligence"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/plugin-code-intelligence

[![npm version](https://img.shields.io/npm/v/@claude-flow/plugin-code-intelligence.svg)](https://www.npmjs.com/package/@claude-flow/plugin-code-intelligence)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/plugin-code-intelligence.svg)](https://www.npmjs.com/package/@claude-flow/plugin-code-intelligence)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 💡 概述

Advanced code analysis and intelligence plugin for Claude Flow V3 - deep code structure understanding, complexity analysis, dependency mapping, refactoring suggestions, and pattern detection.

---

## ⚡ 功能

- **Code Structure Analysis** - AST-based analysis, function/method/class mapping
- **Complexity Analysis** - Cyclomatic complexity, cognitive complexity, maintainability index
- **Dependency Graph** - Module dependency mapping and visualization
- **Code Quality Metrics** - Duplication detection, naming analysis, SOLID compliance
- **Refactoring Suggestions** - AI-powered refactoring recommendations
- **Pattern Detection** - Identify anti-patterns, code smells, and security issues
- **Symbol Resolution** - Cross-file symbol tracking and import analysis
- **Call Graph Analysis** - Function call hierarchy and hot-path identification
- **Technical Debt Calculation** - Quantitative technical debt scoring

### WASM Acceleration

| Component | WASM Package | Speed Improvement |
|-----------|-------------|-------------------|
| Vector search (HNSW) | `micro-hnsw-wasm` | 150x-12,500x faster |
| Dependency graph | `ruvector-gnn-wasm` | Graph Neural Networks |
| Graph partitioning | `ruvector-mincut-wasm` | Min-cut algorithms |
| Learning/adaptation | `sona` | Self-Optimizing Neural Architecture |

### Metrics Tracked

| Metric | Description |
|--------|-------------|
| Cyclomatic Complexity | Code path complexity |
| Cognitive Complexity | Human understandability score |
| Maintainability Index | 0-100 maintainability score |
| Lines of Code (LOC) | Source lines and comments ratio |
| Technical Debt Ratio | Ratio-based debt calculation |
| Halstead Volume | Code volume and difficulty metrics |
| Instability (I) | Package coupling metrics |
| Abstractness (A) | Abstract vs concrete ratio |
| Distance from Main Sequence (D) | Balance between I and A |

---

## 📊 指标

| Metric | Description |
|--------|-------------|
| AST parsing speed | <100ms per 1000 LOC |
| Dependency resolution | <50ms per module |
| Complexity analysis | <10ms per function |
| Pattern detection | <5ms per pattern |

---

## 🏛️ 设计

### Code Analysis

```typescript
import { CodeAnalyzer } from '@claude-flow/plugin-code-intelligence';

const analyzer = new CodeAnalyzer();

// Analyze a single file
const analysis = await analyzer.analyzeFile('src/auth/auth.service.ts');
console.log(analysis.complexity);    // Cyclomatic complexity per function
console.log(analysis.dependencies);  // Import/export graph
console.log(analysis.symbols);       // Variables, functions, classes
console.log(analysis.metrics);       // All quality metrics
```

### Complexity Analysis

```typescript
import { ComplexityAnalyzer } from '@claude-flow/plugin-code-intelligence';

const complexityAnalyzer = new ComplexityAnalyzer();

const report = await complexityAnalyzer.analyze('./src', {
  thresholds: {
    cyclomatic: 10,    // Warn above 10
    cognitive: 15,     // Warn above 15
    lines: 100         // Warn per-function lines
  }
});

// High complexity functions
for (const fn of report.highComplexityFunctions) {
  console.log(`${fn.name} (${fn.file}): cyclomatic=${fn.cyclomatic}`);
}
```

### Dependency Graph

```typescript
import { DependencyMapper } from '@claude-flow/plugin-code-intelligence';

const mapper = new DependencyMapper();

const graph = await mapper.mapDependencies('./src');

// Circular dependencies
console.log(graph.cycles);
// [{ path: ['a.ts', 'b.ts', 'a.ts'], length: 2 }]

// Most depended-on modules
console.log(graph.mostDependedOn);
// [{ module: 'utils.ts', dependents: 25 }]
```

### Code Quality Report

```typescript
import { QualityReporter } from '@claude-flow/plugin-code-intelligence';

const reporter = new QualityReporter();

const report = await reporter.generateReport('./src', {
  metrics: ['all'],
  format: 'detailed'
});

console.log(report.overallScore);     // 0-100
console.log(report.issues);           // Code smells, anti-patterns
console.log(report.recommendations);  // Prioritized fixes
```

### Refactoring Suggestions

```typescript
import { RefactoringEngine } from '@claude-flow/plugin-code-intelligence';

const engine = new RefactoringEngine();

const suggestions = await engine.suggestRefactorings('./src/legacy/', {
  targets: ['complexity', 'duplication', 'naming'],
  maxSuggestions: 10
});

for (const suggestion of suggestions) {
  console.log(`${suggestion.file}: ${suggestion.description}`);
  console.log(`  Impact: ${suggestion.impact} | Effort: ${suggestion.effort}`);
}
```

### Anti-Pattern Detection

```typescript
import { PatternDetector } from '@claude-flow/plugin-code-intelligence';

const detector = new PatternDetector();

const patterns = await detector.detect('./src', {
  patterns: ['god-class', 'long-method', 'feature-envy', 'data-clump'],
  language: 'typescript'
});

for (const pattern of patterns) {
  console.log(`${pattern.type} in ${pattern.file}:${pattern.line} (confidence: ${pattern.confidence}%)`);
}
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Installation

```bash
npx claude-flow@v3alpha plugins install @claude-flow/plugin-code-intelligence
```

### Related Packages

- [@claude-flow/plugin-test-intelligence](../test-intelligence) - Test complement
- [@claude-flow/plugin-perf-optimizer](../perf-optimizer) - Performance optimization
- [ruvector-upstream](../ruvector-upstream) - WASM acceleration dependencies

### License

MIT

---

## 🚀 快速开始

```typescript
import { CodeIntelligencePlugin } from '@claude-flow/plugin-code-intelligence';

const plugin = new CodeIntelligencePlugin();
await plugin.initialize();

// Analyze a directory
const report = await plugin.analyze('./src/');

console.log(`Overall quality: ${report.score}/100`);
console.log(`Issues found: ${report.issues.length}`);
console.log(`Top recommendation: ${report.recommendations[0]}`);
```

---

## ⚙️ 技术栈

- TypeScript
- Node.js
- AST parsing
- RuVector WASM (HNSW, GNN, Mincut, SONA)