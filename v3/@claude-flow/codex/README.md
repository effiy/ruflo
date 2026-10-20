---
title: "@claude-flow/codex"
tags: ["codex", "dual-mode", "openai", "claude", "collaboration", "skills", "templates", "memory", "orchestration"]
category: "@claude-flow/codex"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/codex

[![npm version](https://img.shields.io/npm/v/@claude-flow/codex.svg)](https://www.npmjs.com/package/@claude-flow/codex)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/codex.svg)](https://www.npmjs.com/package/@claude-flow/codex)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-blue.svg)](https://www.typescriptlang.org/)
[![Dual Mode](https://img.shields.io/badge/Dual%20Mode-Claude%20+%20Codex-purple.svg)](https://github.com/ruvnet/claude-flow)

---

## 💡 概述

Codex CLI Adapter for Claude Flow V3 - Dual-mode collaboration system running Claude Code and OpenAI Codex workers in parallel with shared memory, skill injection, template-based pipelines, and cross-platform learning.

---

## ⚡ 功能

- **Dual-Mode Collaboration** - Run Claude Code (architect, reviewer, tester) and Codex (coder, optimizer) in parallel
- **38+ Built-in Skills** - Pre-built specialized skill modules for both platforms
- **37+ Task Templates** - Pre-configured collaboration pipelines for common workflows
- **Shared Memory** - Vector-based memory sharing between platforms
- **Cross-Platform Learning** - Train patterns from successful collaborations
- **Template-Based Pipelines** - Pre-built workflows for features, security, refactoring, bugfixes
- **Skill Injection** - Inject specialized capabilities into agent sessions
- **Worker Coordination** - Dependency-ordered execution with monitoring

### Collaboration Templates

| Template | Workers | Description |
|----------|---------|-------------|
| `feature` | Architect -> Coder -> Tester -> Reviewer | Full feature development |
| `security` | Security Analyst -> Code Scanner -> Reporter | Security audit workflow |
| `refactor` | Architect -> Code Refactorer -> Tester | Code modernization |
| `bugfix` | Researcher -> Code Fixer -> Tester | Bug investigation and fix |
| `docs` | Content Planner -> Writer -> Reviewer | Documentation pipeline |
| `performance` | Profiler -> Optimizer -> Benchmarker | Performance optimization |

### Claude Code Skills (38+)

`architecture-design`, `brainstorming`, `bug-finder`, `code-cleanup`, `code-review-advanced`,
`critical-path`, `dependency-analyzer`, `docstring-writer`, `edge-case-hunter`, `error-handler`,
`git-archaeologist`, `global-refactor`, `interface-designer`, `linter-custom`, `migration-helper`,
`module-doc-writer`, `naming-review`, `package-update`, `pattern-review`, `performance-auditor`,
`protocol-design`, `refactor-wizard`, `regex-generator`, `regex-review`, `security-auditor`,
`test-generator`, `type-generator`, `ui-auditor`, ...

### Codex Skills (37+)

`api-builder`, `benchmark-writer`, `ci-checker`, `cli-builder`, `code-interpreter`,
`code-summarizer`, `code-translator`, `data-processor`, `db-schema`, `dev-commands`,
`diagram-generator`, `docker-compose`, `documentation-generator`, `env-configurator`,
`error-message`, `git-config`, `github-actions`, `integration-builder`, `json-converter`,
`mermaid-generator`, `node-builder`, `npm-package`, `proto-builder`, `query-builder`,
`sdk-builder`, `server-builder`, `session-manager`, `sql-builder`, `test-builder`,
`typescript-master`, `web-builder`, `yaml-configurator`, ...

---

## 📊 指标

### Model Tiers (ADR-026)

| Tier | Handler | Latency | Cost | Use Cases |
|------|---------|---------|------|-----------|
| **1** | Deterministic codemod | ~1ms | $0 | Structural transforms (no LLM) |
| **2** | Haiku | ~500ms | $0.0002 | Simple tasks, low complexity |
| **3** | Sonnet/Opus | 2-5s | $0.003-0.015 | Complex reasoning, architecture |

### Worker Dependency Levels

```
Level 0: [Architect]           # No dependencies
Level 1: [Coder, Tester]       # Depends on Architect
Level 2: [Reviewer]            # Depends on Coder + Tester
Level 3: [Optimizer]           # Depends on Reviewer
```

### Platform Strengths

| Task Type | Preferred Platform | Reason |
|-----------|-------------------|--------|
| Architecture & Design | Claude | Strong reasoning, system thinking |
| Implementation | Codex | Fast code generation |
| Security Review | Claude | Careful analysis, threat modeling |
| Performance Optimization | Codex | Code-level optimizations |
| Testing Strategy | Claude | Coverage analysis, edge cases |
| Refactoring | Codex | Bulk code transformations |

---

## 🏛️ 设计

### CLI Commands

```bash
# Run a collaboration template
npx @claude-flow/codex dual run feature --task "Add user authentication with OAuth"
npx @claude-flow/codex dual run security --target "./src"
npx @claude-flow/codex dual run refactor --target "./src/legacy"

# Custom multi-platform swarm
npx @claude-flow/codex dual run \
  --worker "claude:architect:Design the API structure" \
  --worker "codex:coder:Implement REST endpoints" \
  --worker "claude:tester:Write integration tests" \
  --worker "codex:reviewer:Review code quality" \
  --namespace "api-feature"

# Check collaboration status
npx @claude-flow/codex dual status

# List available templates
npx @claude-flow/codex dual templates

# List available skills
npx @claude-flow/codex dual skills
```

### Skill Management

```typescript
import { CodexSkillManager } from '@claude-flow/codex';

const skills = new CodexSkillManager();

// List all available skills
const allSkills = skills.listAll();

// List by platform
const claudeSkills = skills.listByPlatform('claude');
const codexSkills = skills.listByPlatform('codex');

// Run a skill directly
const result = await skills.runSkill('claude', 'security-auditor', {
  target: './src/auth'
});

// Inject skills into agents
const agent = skills.injectSkills('claude', ['security-auditor', 'code-review-advanced']);
```

### Template System

```typescript
import { CodexTemplateRunner } from '@claude-flow/codex';

const runner = new CodexTemplateRunner();

// Run a pre-built template
const result = await runner.runTemplate('feature', {
  task: 'Add user authentication',
  language: 'typescript',
  testFramework: 'jest'
});

// List templates
const templates = runner.listTemplates();

// Create custom template
runner.createTemplate('my-workflow', {
  workers: [
    { platform: 'claude', role: 'architect', task: 'Design solution' },
    { platform: 'codex', role: 'coder', task: 'Implement solution' },
    { platform: 'claude', role: 'tester', task: 'Write tests' }
  ]
});
```

### Dual-Mode Orchestrator

```typescript
import { DualModeOrchestrator, CollaborationTemplates } from '@claude-flow/codex';

const orchestrator = new DualModeOrchestrator({
  namespace: 'my-feature',
  memoryBackend: 'hybrid',
  maxConcurrency: 4
});

// Use pre-built template
const workers = CollaborationTemplates.featureDevelopment('Add OAuth login');

// Run collaboration
const results = await orchestrator.runCollaboration(workers, 'Implement OAuth feature');

// Access shared memory
const designDocs = await orchestrator.getMemory('design-decisions');

// Get worker results
for (const result of results) {
  console.log(`${result.worker}: ${result.status}`);
}
```

### Shared Memory

```typescript
// All workers share state via the shared memory namespace
import { SharedMemory } from '@claude-flow/codex';

const memory = new SharedMemory('collaboration');

// Store context for cross-platform sharing
await memory.store('design-decisions', {
  pattern: 'Repository pattern',
  database: 'PostgreSQL',
  auth: 'OAuth 2.0 + JWT'
});

// Search for patterns across all workers
const patterns = await memory.search('authentication patterns');

// Retrieve specific findings
const findings = await memory.retrieve('security-findings');
```

### Programmatic API

```typescript
import { CodexCLI } from '@claude-flow/codex';

const codex = new CodexCLI({
  model: 'auto',
  platform: 'auto',
  skills: ['typescript', 'testing'],
  memory: { backend: 'hybrid' }
});

// Run with auto-routing
await codex.run('Write a TypeScript function to parse CSV files');

// Run on specific platform
await codex.runOnClaude('Review this security configuration');
await codex.runOnCodex('Optimize this database query');

// Batch mode
const results = await codex.batch([
  { task: 'Add TypeScript types' },
  { task: 'Write unit tests' },
  { task: 'Document the API' }
]);
```

### Cross-Platform Learning

```typescript
import { CrossPlatformLearning } from '@claude-flow/codex';

const learning = new CrossPlatformLearning();

// After successful collaboration
await learning.trainFromCollaboration('feature-implementation-123');

// Share patterns between platforms
await learning.sharePatterns('claude', 'codex', 'successful-auth-pattern');

// Get learned patterns
const patterns = await learning.getPatterns({
  platform: 'both',
  category: 'authentication'
});
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Installation

```bash
npm install @claude-flow/codex
```

### Environment Setup

```bash
# Required API keys
ANTHROPIC_API_KEY=sk-ant-...
OPENAI_API_KEY=sk-...

# Optional configuration
CODEX_DEFAULT_PLATFORM=auto
CODEX_MEMORY_BACKEND=hybrid
CODEX_LOG_LEVEL=info
```

### CLI Integration

```bash
# Dual run via CLI
npx claude-flow@v3alpha codex dual run feature --task "Add user auth"

# List all
npx claude-flow@v3alpha codex dual skills
npx claude-flow@v3alpha codex dual templates
npx claude-flow@v3alpha codex dual status
```

### Related Packages

- [@claude-flow/providers](../providers) - Multi-provider LLM integration
- [@claude-flow/integration](../integration) - Deep agentic-flow integration
- [@claude-flow/plugins](../plugins) - Plugin SDK with worker management

### License

MIT

---

## 🚀 快速开始

```typescript
import { CodexCLI, DualModeOrchestrator } from '@claude-flow/codex';

// Simple CLI usage
const codex = new CodexCLI();
const result = await codex.run('Write a hello world in TypeScript');

// Dual-mode collaboration
const orchestrator = new DualModeOrchestrator({
  namespace: 'quick-start'
});

const results = await orchestrator.runCollaboration([
  { platform: 'claude', role: 'architect', task: 'Design a REST API' },
  { platform: 'codex', role: 'coder', task: 'Implement it' }
], 'Build a simple API');
```

---

## ⚙️ 技术栈

- TypeScript
- Node.js
- Claude Code API (Anthropic)
- OpenAI Codex CLI
- AgentDB shared memory