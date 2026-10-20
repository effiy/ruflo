---
title: "@claude-flow/testing"
tags: ["testing", "jest", "tdd", "mocking", "swarm", "coverage", "integration"]
category: "@claude-flow/testing"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/testing

[![npm version](https://img.shields.io/npm/v/@claude-flow/testing.svg)](https://www.npmjs.com/package/@claude-flow/testing)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/testing.svg)](https://www.npmjs.com/package/@claude-flow/testing)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 💡 概述

Testing framework for Claude Flow V3 - TDD-oriented test infrastructure with swarm testing, test intelligence, coverage analysis, and fixture management.

---

## ⚡ 功能

- **Jest Configuration** - Pre-built Jest configs for unit, integration, and E2E tests
- **Swarm Testing** - Test orchestration across multiple agents in parallel
- **Test Intelligence** - Smart test selection, gap analysis, and coverage optimization
- **Fixture Management** - Template-based test fixture generation
- **Mocking Utilities** - Pre-built mocks for Claude Flow components
- **Coverage Analysis** - Coverage reports with gap detection
- **Benchmark Integration** - Performance benchmarks as tests
- **CI Integration** - GitHub Actions and CI pipelines

### Test Templates

| Template | Description |
|----------|-------------|
| `unit` | Unit test with mocking |
| `integration` | Integration test with real dependencies |
| `e2e` | End-to-end test |
| `snapshot` | Snapshot test |
| `benchmark` | Performance benchmark test |
| `swarm` | Multi-agent swarm test |
| `security` | Security-focused test |
| `api` | API endpoint test |

---

## 📊 指标

### Test Configuration Presets

| Preset | Coverage Target | File Pattern |
|--------|----------------|--------------|
| `unit` | 90% | `*.test.ts` |
| `integration` | 80% | `*.integration.test.ts` |
| `e2e` | 70% | `*.e2e.test.ts` |

### Coverage Thresholds

```typescript
{
  branches: 85,
  functions: 90,
  lines: 85,
  statements: 85
}
```

---

## 🏛️ 设计

### Jest Configuration

```typescript
import { createJestConfig, Presets } from '@claude-flow/testing';

const config = createJestConfig({
  preset: Presets.unit,
  coverage: true
});

export default config;
```

### Swarm Testing

```typescript
import { SwarmTester } from '@claude-flow/testing';

const tester = new SwarmTester({
  agents: ['tester-1', 'tester-2', 'tester-3'],
  strategy: 'parallel',
  timeout: 30000
});

const results = await tester.runTests(['src/auth', 'src/api'], {
  reporter: 'detailed'
});

// Results include per-agent coverage and execution time
for (const [agentId, result] of Object.entries(results.agents)) {
  console.log(`${agentId}: ${result.passed}/${result.total} passed`);
}
```

### Test Intelligence

```typescript
import { TestIntelligence } from '@claude-flow/testing';

const intelligence = new TestIntelligence();

// Analyze test gaps
const gaps = await intelligence.analyzeTestGaps('./src');

// Suggest tests
const suggestions = await intelligence.suggestTests('./src/auth/auth.service.ts');

// Smart test selection (only run affected tests)
const testsToRun = await intelligence.selectTests(['src/auth/auth.service.ts']);
```

### Mocking Utilities

```typescript
import {
  mockAgent,
  mockMemory,
  mockProvider,
  mockMCPTool,
  mockSwarm
} from '@claude-flow/testing';

// Create mock agent
const agent = mockAgent({
  type: 'coder',
  name: 'test-coder'
});

// Create mock memory with test data
const memory = mockMemory({
  entries: [
    { key: 'pattern-1', value: 'test pattern' }
  ]
});

// Create mock LLM provider
const provider = mockProvider('anthropic', {
  responses: ['function add(a, b) { return a + b; }']
});
```

### Fixture Management

```typescript
import { FixtureManager, FixtureTemplate } from '@claude-flow/testing';

const fixtures = new FixtureManager('./test/fixtures');

// Generate fixture from template
const userFixture = await fixtures.generate('user', {
  template: FixtureTemplate.user,
  count: 5
});

// Load fixture
const users = await fixtures.load('users.json');

// Clean up
await fixtures.cleanup();
```

### Coverage Analysis

```typescript
import { CoverageAnalyzer } from '@claude-flow/testing';

const analyzer = new CoverageAnalyzer();

// Analyze coverage report
const report = await analyzer.analyze('./coverage/coverage-final.json');

// Check against thresholds
const pass = analyzer.checkThresholds(report, {
  branches: 85,
  functions: 90,
  lines: 85
});

// Find gaps
const gaps = analyzer.findGaps(report);
for (const gap of gaps) {
  console.log(`Uncovered: ${gap.file}:${gap.line}`);
}
```

### Benchmark Tests

```typescript
import { BenchmarkTest } from '@claude-flow/testing';

BenchmarkTest('HNSW search performance', async () => {
  // Setup
  const index = await createHNSWIndex();

  // Run benchmark
  const result = await BenchmarkTest.run({
    iterations: 100,
    warmup: 10,
    fn: async () => {
      return await index.search(queryVector, 10);
    }
  });

  // Assert performance
  expect(result.p95).toBeLessThan(5); // ms
  expect(result.opsPerSecond).toBeGreaterThan(1000);
});
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Installation

```bash
npm install --save-dev @claude-flow/testing
```

### Quick Commands

```bash
# Run all tests
npx jest --config jest.config.ts

# Run with coverage
npx jest --config jest.config.ts --coverage

# Run specific test suite
npx jest --config jest.config.ts --testPathPattern="src/auth"

# Run affected tests only
npx claude-flow@v3alpha testing smart-select --changed-files
```

### Related Packages

- [@claude-flow/plugin-test-intelligence](../../plugins/test-intelligence) - Test intelligence plugin
- [@claude-flow/security](../security) - Security testing

### License

MIT

---

## 🚀 快速开始

```typescript
// jest.config.ts
import { createJestConfig, Presets } from '@claude-flow/testing';

export default createJestConfig({
  preset: Presets.unit,
  coverage: true
});

// __tests__/example.test.ts
import { mockAgent, mockMemory } from '@claude-flow/testing';
import { MyService } from '../src/my-service';

describe('MyService', () => {
  it('should work', () => {
    const agent = mockAgent({ type: 'coder' });
    const memory = mockMemory();
    const service = new MyService(agent, memory);

    expect(service.doWork()).toBe(true);
  });
});
```

---

## ⚙️ 技术栈

- TypeScript
- Jest
- Node.js