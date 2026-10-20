---
title: "Claude Flow V3 — Modular AI Agent Coordination System"
tags: ["v3", "agent", "swarm", "ddd", "mcp", "typescript", "vitest", "hnsw", "sona"]
category: "v3"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# Claude Flow V3

> **Modular AI Agent Coordination System** - A complete reimagining of Claude-Flow with 15-agent hierarchical mesh swarm coordination.

[![Version](https://img.shields.io/badge/version-3.0.0--alpha.1-blue.svg)](https://github.com/ruvnet/claude-flow)
[![Node.js](https://img.shields.io/badge/node-%3E%3D20.0.0-green.svg)](https://nodejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.3+-blue.svg)](https://www.typescriptlang.org/)
[![License](https://img.shields.io/badge/license-MIT-purple.svg)](../LICENSE)

---

## 💡 概述

Claude Flow V3 is a next-generation AI agent coordination system built on 10 Architecture Decision Records (ADRs). It provides a modular, security-first, high-performance platform for orchestrating multi-agent swarms with hierarchical mesh topology.

V3 represents a complete architectural overhaul:
- **10x faster testing** with Vitest
- **150x-12,500x faster search** with HNSW indexing
- **2.49x-7.47x Flash Attention speedup**
- **50-75% memory reduction**

---

## ⚡ 功能

### Core Capabilities

- **15-Agent Hierarchical Mesh** - Queen-led coordination with specialized worker agents
- **Domain-Driven Design** - Clean bounded contexts with separation of concerns
- **Plugin Architecture** - Microkernel pattern for extensibility
- **MCP-First API** - Consistent interfaces across all modules
- **Event Sourcing** - Full audit trail for state changes
- **Hybrid Memory Backend** - SQLite + AgentDB for optimal performance

### Security

- **CVE Remediation** - All known vulnerabilities addressed
- **Input Validation** - Zod-based schema validation
- **Secure ID Generation** - Cryptographic random IDs
- **Path Security** - Traversal protection
- **SQL Injection Prevention** - Parameterized queries

---

## 📊 指标

| Metric | Target | Achieved |
|--------|--------|----------|
| Event Bus (100k events) | <50ms | ~6ms |
| Map Lookup (100k gets) | <20ms | ~16ms |
| Array.find vs Map O(1) | N/A | 978x speedup |
| Flash Attention | 2.49x-7.47x | Validated |
| AgentDB Search | 150x-12,500x | HNSW indexed |

### Performance Targets

| Category | Metric | Target |
|----------|--------|--------|
| **Search** | AgentDB HNSW | 150x-12,500x faster |
| **Attention** | Flash Attention | 2.49x-7.47x speedup |
| **Memory** | Reduction | 50-75% |
| **Code** | Total lines | <5,000 |
| **Startup** | Cold start | <500ms |
| **Learning** | SONA adaptation | <0.05ms |

---

## 🏛️ 设计

### Architecture Decision Records (ADRs)

| ADR | Decision |
|-----|----------|
| ADR-001 | Adopt agentic-flow as core foundation |
| ADR-002 | Domain-Driven Design structure |
| ADR-003 | Single coordination engine (UnifiedSwarmCoordinator) |
| ADR-004 | Plugin-based architecture (microkernel) |
| ADR-005 | MCP-first API design |
| ADR-006 | Unified memory service (AgentDB) |
| ADR-007 | Event sourcing for state changes |
| ADR-008 | Vitest over Jest (10x faster) |
| ADR-009 | Hybrid memory backend default |
| ADR-010 | Remove Deno support (Node.js 20+ only) |

### Module Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                     @claude-flow/v3-monorepo                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │   security   │  │    memory    │  │    swarm     │          │
│  │  CVE fixes   │  │   AgentDB    │  │ 15-agent     │          │
│  │  validation  │  │   HNSW       │  │ coordination │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │ integration  │  │  performance │  │    neural    │          │
│  │ agentic-flow │  │ Flash Attn   │  │   SONA       │          │
│  │  bridge      │  │ benchmarks   │  │  learning    │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │     cli      │  │   testing    │  │  deployment  │          │
│  │  commands    │  │ TDD London   │  │   release    │          │
│  │  prompts     │  │   School     │  │    CI/CD     │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                       shared                             │   │
│  │  types • events • core • hooks • resilience • plugins   │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📁 目录

```
v3/
├── @claude-flow/                    # Modular packages
│   ├── security/                    # Security module (Password hashing, validators)
│   ├── memory/                      # Memory module (AgentDB, HNSW, hybrid backend, cache)
│   ├── swarm/                       # Swarm coordination (unified coordinator, topology, consensus)
│   ├── integration/                 # agentic-flow integration (bridge, agent/sona adapters)
│   ├── performance/                 # Performance module (benchmark framework, Flash Attention)
│   ├── neural/                      # Neural/SONA module (algorithms, modes)
│   ├── cli/                         # CLI module (bin, commands)
│   ├── testing/                     # Testing framework (fixtures, mocks, helpers, regression)
│   ├── shared/                      # Shared utilities (types, events, core, hooks, resilience, plugins)
│   └── deployment/                  # Deployment module (release management)
│
├── mcp/                             # MCP Server (server.ts, tools, transport)
├── __tests__/                       # Integration tests
├── docs/                            # Documentation (guides, implementation)
├── helpers/                         # Cross-platform helpers
├── scripts/                         # Utility scripts
├── index.ts                         # Main entry point
├── swarm.config.ts                  # Swarm configuration
├── vitest.config.ts                 # Test configuration
└── package.json                     # Monorepo package
```

---

## 🔗 资源

### Documentation
- [Docs Overview](./docs/README.md)
- [Implementation Details](./docs/implementation/)
- [User Guides](./docs/guides/)
- [Helper System](./helpers/README.md)

### Modules
- [@claude-flow/security](./@claude-flow/security/)
- [@claude-flow/memory](./@claude-flow/memory/)
- [@claude-flow/swarm](./@claude-flow/swarm/)
- [@claude-flow/integration](./@claude-flow/integration/)
- [@claude-flow/performance](./@claude-flow/performance/)
- [@claude-flow/neural](./@claude-flow/neural/)
- [@claude-flow/cli](./@claude-flow/cli/)
- [@claude-flow/testing](./@claude-flow/testing/)
- [@claude-flow/shared](./@claude-flow/shared/)
- [@claude-flow/deployment](./@claude-flow/deployment/)

### MCP Tools
- [Agent Tools](./mcp/tools/agent-tools.ts)
- [Swarm Tools](./mcp/tools/swarm-tools.ts)
- [Memory Tools](./mcp/tools/memory-tools.ts)
- [Hooks Tools](./mcp/tools/hooks-tools.ts)

### External
- [GitHub Repository](https://github.com/ruvnet/claude-flow)
- [agentic-flow Integration](https://github.com/ruvnet/agentic-flow)
- [AgentDB](https://github.com/ruvnet/agentdb)

---

## 🚀 快速开始

### Installation

```bash
# Clone the repository
git clone https://github.com/ruvnet/claude-flow.git
cd claude-flow/v3

# Install dependencies
pnpm install

# Build all modules
pnpm build
```

### Usage

```typescript
import { initializeV3Swarm } from '@claude-flow/v3';

// Initialize the swarm
const swarm = await initializeV3Swarm();

// Spawn agents
await swarm.spawnAllAgents();

// Submit a task
const task = swarm.submitTask({
  type: 'implementation',
  title: 'Implement feature X',
  description: 'Detailed description...',
  domain: 'core',
  phase: 'phase-2-core',
  priority: 'high'
});

// Wait for completion
const result = await swarm.waitForTask(task.id);
```

### Testing

```bash
# Run all tests
pnpm test

# Run integration tests
pnpm test:integration

# Run specific module tests
pnpm test:memory
pnpm test:swarm
pnpm test:security

# Run benchmarks
pnpm bench

# Quick benchmark (no dependencies)
node scripts/quick-benchmark.mjs

# Coverage report
pnpm test:coverage
```

---

## ⚙️ 技术栈

- **Node.js**: >=20.0.0
- **pnpm**: >=8.0.0
- **TypeScript**: >=5.3.0
- Vitest (test runner)
- HNSW (vector indexing)
- SONA (neural learning)
- MCP Protocol
- Event Sourcing

---

**Built with the SPARC methodology and 15-agent hierarchical mesh coordination.**