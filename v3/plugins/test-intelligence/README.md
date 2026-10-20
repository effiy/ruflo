---
title: "@claude-flow/plugin-test-intelligence"
tags: ["testing", "test-intelligence", "coverage", "mutation-testing", "flaky-detection", "gap-analysis"]
category: "plugins/test-intelligence"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/plugin-test-intelligence

[![npm version](https://img.shields.io/npm/v/@claude-flow/plugin-test-intelligence.svg)](https://www.npmjs.com/package/@claude-flow/plugin-test-intelligence)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/plugin-test-intelligence.svg)](https://www.npmjs.com/package/@claude-flow/plugin-test-intelligence)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 💡 概述

Intelligent test generation and gap analysis plugin for Claude Flow V3 - AI-powered test coverage analysis, test generation, mutation testing, and flaky test detection with RuVector WASM acceleration.

---

## ⚡ 功能

- **Intelligent Test Generation** - AI-powered test case generation based on code analysis
- **Coverage Gap Analysis** - Identify untested code paths, branches, and edge cases
- **Mutation Testing** - Measure test quality by introducing and detecting code mutations
- **Test Impact Analysis** - Determine which tests need to run for changed code
- **Flaky Test Detection** - Identify and analyze flaky tests with pattern recognition
- **Test Prioritization** - Rank tests by importance, coverage, and historical failure rate
- **Test Suite Optimization** - Remove redundant tests and optimize execution order

### WASM Acceleration

| Component | WASM Package | Speed Improvement |
|-----------|-------------|-------------------|
| Pattern matching | `ruvector-learning-wasm` | RL-based optimization |
| Dependency graph | `ruvector-gnn-wasm` | Graph Neural Networks |
| Learning/adaptation | `sona` | Self-Optimizing Neural Architecture |

---

## 📊 指标

| Metric | Target |
|--------|--------|
| Test generation relevance | >85% |
| Mutation score improvement | >25% |
| Flaky detection accuracy | >95% |
| Coverage gap precision | >90% |

---

## 🏛️ 设计

### Test Generation

```typescript
import { TestGenerator } from '@claude-flow/plugin-test-intelligence';

const generator = new TestGenerator({ aiProvider: 'default' });

// Generate tests for a file
const tests = await generator.generateTests('src/auth/auth.service.ts', {
  framework: 'jest',
  coverage: { branches: 90, functions: 95 },
  mockExternalDependencies: true,
  includeEdgeCases: true
});

// Generate integration tests
const integrationTests = await generator.generateIntegrationTests(
  'src/api/user.routes.ts',
  { database: true, auth: true }
);
```

### Coverage Gap Analysis

```typescript
import { GapAnalyzer } from '@claude-flow/plugin-test-intelligence';

const analyzer = new GapAnalyzer();

// Analyze coverage gap
const gaps = await analyzer.analyze({
  sourceDir: './src',
  coverageFile: './coverage/coverage-final.json',
  minBranchCoverage: 85,
  riskWeighting: true
});

// Gap report with recommendations
for (const gap of gaps) {
  console.log(`${gap.file}:${gap.line} - ${gap.severity}`);
  console.log(`  Suggestion: ${gap.suggestion}`);
}
```

### Mutation Testing

```typescript
import { MutationTester } from '@claude-flow/plugin-test-intelligence';

const mutator = new MutationTester();

// Run mutation testing
const results = await mutator.run({
  target: 'src/core/',
  mutations: ['arithmetic', 'logical', 'conditional', 'return-values'],
  timeout: 300000  // 5 minutes
});

console.log(`Mutation score: ${(results.killed / results.total * 100).toFixed(1)}%`);
console.log(`Survivors: ${results.survivors.map(s => s.description).join(', ')}`);
```

### Test Impact Analysis

```typescript
import { ImpactAnalyzer } from '@claude-flow/plugin-test-intelligence';

const analyzer = new ImpactAnalyzer();

// Get affected tests for changed files
const impacted = await analyzer.analyzeImpact({
  changedFiles: ['src/auth/auth.service.ts'],
  testDir: './test',
  dependencyGraph: true
});

console.log(`Need to run ${impacted.tests.length} of ${impacted.totalTests} tests`);
```

### Flaky Test Detection

```typescript
import { FlakyDetector } from '@claude-flow/plugin-test-intelligence';

const detector = new FlakyDetector();

// Analyze test history
const flaky = await detector.analyze({
  testResultsDir: './test-results',
  minRuns: 5,
  flakyThreshold: 0.3  // 30% inconsistent
});

for (const test of flaky.flakyTests) {
  console.log(`${test.name}: ${test.flakyScore} (${test.passRate} pass rate)`);
}
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Installation

```bash
npx claude-flow@v3alpha plugins install @claude-flow/plugin-test-intelligence
```

### Quick Commands

```bash
# Analyze gaps
npx claude-flow@v3alpha hooks coverage-gaps --format table

# Generate tests
npx claude-flow@v3alpha hooks coverage-suggest --path src/auth/

# Route based on coverage
npx claude-flow@v3alpha hooks coverage-route --task "Add auth tests"
```

### Related Packages

- [@claude-flow/testing](../../@claude-flow/testing) - Core testing framework
- [@claude-flow/plugin-code-intelligence](../code-intelligence) - Code analysis complement
- [ruvector-upstream](../ruvector-upstream) - WASM acceleration dependencies

### License

MIT

---

## 🚀 快速开始

```typescript
import { TestIntelligencePlugin } from '@claude-flow/plugin-test-intelligence';

const plugin = new TestIntelligencePlugin();
await plugin.initialize();

// Analyze and generate
const analysis = await plugin.analyze('./src/');
const tests = await plugin.generateTests('./src/core/');

console.log(`Found ${analysis.gaps.length} coverage gaps`);
console.log(`Generated ${tests.length} test files`);
```

---

## ⚙️ 技术栈

- TypeScript
- Node.js
- RuVector WASM (GNN, Learning, SONA)
- Jest/Vitest