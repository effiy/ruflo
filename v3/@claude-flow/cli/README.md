---
title: "@claude-flow/cli"
tags: ["cli", "command-line", "v3", "orchestration", "swarm", "memory", "hooks", "mcp", "agents"]
category: "@claude-flow/cli"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/cli

[![npm version](https://img.shields.io/npm/v/@claude-flow/cli.svg)](https://www.npmjs.com/package/@claude-flow/cli)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/cli.svg)](https://www.npmjs.com/package/@claude-flow/cli)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-blue.svg)](https://www.typescriptlang.org/)
[![V3](https://img.shields.io/badge/V3-Production-green.svg)](https://github.com/ruvnet/claude-flow)
[![Commands](https://img.shields.io/badge/Commands-26-orange.svg)](https://github.com/ruvnet/claude-flow)

---

## 💡 概述

Main CLI for Claude Flow V3 - 26 commands, 140+ subcommands. Full ecosystem: agent lifecycle, swarm orchestration, memory/HNSW search, MCP server management, neural learning (SONA/MoE/EWC++), hive-mind consensus (Byzantine/Raft/Gossip/CRDT), hooks + 12 workers, performance benchmarking, security scanning, and plugin registry.

---

## ⚡ 功能

### 26 Commands Overview

| Command | Subcommands | Description |
|---------|-------------|-------------|
| `init` | 4 | Project initialization with wizard, presets, skills, hooks |
| `agent` | 8 | Agent lifecycle (spawn, list, status, stop, metrics, pool, health, logs) |
| `swarm` | 6 | Multi-agent swarm coordination and orchestration |
| `memory` | 11 | AgentDB memory with HNSW vector search (measured ~1.9x-4.7x vs brute force above crossover) |
| `mcp` | 9 | MCP server management and tool execution |
| `task` | 6 | Task creation, assignment, and lifecycle |
| `session` | 7 | Session state management and persistence |
| `config` | 7 | Configuration management and provider setup |
| `status` | 3 | System status monitoring with watch mode |
| `start` | 3 | Service startup and quick launch |
| `workflow` | 6 | Workflow execution and template management |
| `hooks` | 17 | Self-learning hooks + 12 background workers |
| `hive-mind` | 6 | Queen-led Byzantine fault-tolerant consensus |

### Advanced Commands

| Command | Subcommands | Description |
|---------|-------------|-------------|
| `daemon` | 5 | Background worker daemon |
| `neural` | 5 | Neural pattern training |
| `security` | 6 | Security scanning (scan, audit, cve, threats, validate, report) |
| `performance` | 5 | Performance profiling |
| `providers` | 5 | AI providers management |
| `plugins` | 5 | Plugin management |
| `deployment` | 5 | Deployment management |
| `embeddings` | 4 | Vector embeddings |
| `claims` | 4 | Claims-based authorization |
| `migrate` | 5 | V2 to V3 migration |
| `process` | 4 | Background process management |
| `doctor` | 1 | System diagnostics |
| `completions` | 4 | Shell completions |
| `codex` | 1+ | Codex dual-mode CLI adapter |

---

## 📊 指标

### Key Agent Types (60+)

| Category | Agent Types |
|----------|-------------|
| **Core** | `coder`, `reviewer`, `tester`, `planner`, `researcher` |
| **V3 Specialized** | `security-architect`, `security-auditor`, `memory-specialist`, `performance-engineer` |
| **Swarm** | `hierarchical-coordinator`, `mesh-coordinator`, `adaptive-coordinator`, `collective-intelligence-coordinator` |
| **Consensus** | `byzantine-coordinator`, `raft-manager`, `gossip-coordinator`, `consensus-builder`, `crdt-synchronizer` |
| **GitHub** | `github-modes`, `pr-manager`, `code-review-swarm`, `issue-tracker`, `release-manager` |
| **SPARC** | `sparc-coord`, `sparc-coder`, `specification`, `pseudocode`, `architecture`, `refinement` |

### Routing Codes

| Code | Task | Agents |
|------|------|--------|
| 1 | Bug Fix | coordinator, researcher, coder, tester |
| 3 | Feature | coordinator, architect, coder, tester, reviewer |
| 5 | Refactor | coordinator, architect, coder, reviewer |
| 7 | Performance | coordinator, perf-engineer, coder |
| 9 | Security | coordinator, security-architect, auditor |
| 11 | Memory | coordinator, memory-specialist, perf-engineer |

### Model Routing (ADR-026)

| Tier | Handler | Latency | Cost |
|------|---------|---------|------|
| 1 | Deterministic codemod | ~1ms | $0 |
| 2 | Haiku | ~500ms | $0.0002 |
| 3 | Sonnet/Opus | 2-5s | $0.003-0.015 |

---

## 🏛️ 设计

### Quick CLI Examples

```bash
# Initialize
npx claude-flow@v3alpha init --wizard

# Daemon
npx claude-flow@v3alpha daemon start

# Agent
npx claude-flow@v3alpha agent spawn -t coder --name my-coder

# Swarm
npx claude-flow@v3alpha swarm init --topology hierarchical --max-agents 8 --strategy specialized

# Memory (HNSW-indexed)
npx claude-flow@v3alpha memory search --query "authentication patterns"
npx claude-flow@v3alpha memory store --key "pattern-auth" --value "JWT with refresh tokens" --namespace patterns
npx claude-flow@v3alpha memory retrieve --key "pattern-auth" --namespace patterns
npx claude-flow@v3alpha memory list --namespace patterns --limit 10

# Hooks
npx claude-flow@v3alpha hooks pre-task --description "[task]"
npx claude-flow@v3alpha hooks post-task --task-id "[id]" --success true
npx claude-flow@v3alpha hooks session-start --session-id "[id]"
npx claude-flow@v3alpha hooks worker list
npx claude-flow@v3alpha hooks worker dispatch --trigger audit
npx claude-flow@v3alpha hooks route --task "[task]"
npx claude-flow@v3alpha hooks coverage-gaps --format table

# Diagnostics
npx claude-flow@v3alpha doctor --fix
npx claude-flow@v3alpha security scan --depth full
npx claude-flow@v3alpha performance benchmark --suite all

# Codex dual-mode
npx claude-flow@v3alpha codex dual run feature --task "Add user auth"
npx claude-flow@v3alpha codex dual skills
npx claude-flow@v3alpha codex dual templates

# Hive-mind
npx claude-flow@v3alpha hive-mind init --topology hierarchical-mesh --strategy byzantine

# Migration
npx claude-flow@v3alpha migrate run --backup
```

### Hooks System (17 hooks + 12 workers)

| Hook | Description |
|------|-------------|
| `pre-edit`, `post-edit` | Tool lifecycle |
| `pre-command`, `post-command` | Command lifecycle |
| `pre-task`, `post-task` | Task lifecycle |
| `session-start`, `session-end`, `session-restore` | Session management |
| `route`, `explain` | Intelligence routing |
| `pretrain`, `build-agents` | Neural training |
| `transfer` | Pattern transfer |
| `intelligence` | RuVector intelligence |
| `worker` | Background worker management |

| Worker | Priority |
|--------|----------|
| `audit` | critical |
| `optimize` | high |
| `ultralearn` | normal |
| `predict` | normal |
| `map` | normal |
| `deepdive` | normal |
| `document` | normal |
| `refactor` | normal |
| `benchmark` | normal |
| `testgaps` | normal |
| `consolidate` | low |
| `preload` | low |

### Swarm Topologies

| Topology | Description |
|----------|-------------|
| `hierarchical` | Queen controls workers (anti-drift for small teams) |
| `hierarchical-mesh` | Queen + peer communication (10+ agents) |
| `mesh` | Fully connected peer network |
| `adaptive` | Dynamic topology switching |

### Consensus Strategies

| Strategy | Fault Tolerance |
|----------|----------------|
| `byzantine` | f < n/3 faulty |
| `raft` | f < n/2 |
| `gossip` | Eventually consistent |
| `crdt` | Conflict-free |

### Intelligence Pipeline

1. **RETRIEVE** - Fetch patterns via HNSW
2. **JUDGE** - Evaluate with verdicts
3. **DISTILL** - Extract learnings via LoRA
4. **CONSOLIDATE** - Prevent forgetting via EWC++

### Key Environment Variables

```bash
# Configuration
CLAUDE_FLOW_CONFIG=./claude-flow.config.json
CLAUDE_FLOW_LOG_LEVEL=info

# Provider API Keys
ANTHROPIC_API_KEY=sk-ant-...
OPENAI_API_KEY=sk-...
GOOGLE_API_KEY=...

# MCP Server
CLAUDE_FLOW_MCP_PORT=3000
CLAUDE_FLOW_MCP_HOST=localhost
CLAUDE_FLOW_MCP_TRANSPORT=stdio

# Memory
CLAUDE_FLOW_MEMORY_BACKEND=hybrid
CLAUDE_FLOW_MEMORY_PATH=./data/memory
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Quick Setup

```bash
# MCP server
claude mcp add claude-flow -- npx -y ruflo@latest mcp start

# Start daemon
npx claude-flow@v3alpha daemon start

# Doctor check
npx claude-flow@v3alpha doctor --fix
```

### Publishing

The normal public release train is exactly THREE packages: `@claude-flow/cli`, `claude-flow`, and `ruflo`. Four `@claude-flow/*` components are bundled: `security`, `codex`, `mcp`, `plugin-agent-federation`. All other `@claude-flow/*` deps resolve from the registry at the version `package.json` pins.

### Related Packages

- [@claude-flow/codex](../codex) - Dual-mode Claude + Codex collaboration
- [@claude-flow/guidance](../guidance) - Governance control plane
- [@claude-flow/security](../security) - Security module (bundled)
- [@claude-flow/mcp](../mcp) - MCP server (bundled)
- [@claude-flow/shared](../shared) - Shared utilities

### Documentation

- [Project Homepage](https://github.com/ruvnet/claude-flow)
- [Issues](https://github.com/ruvnet/claude-flow/issues)

### License

MIT

---

## 🚀 快速开始

```bash
# Install and initialize
npm install -g @claude-flow/cli
claude-flow init --wizard

# Quick start
npx claude-flow@v3alpha init --wizard
npx claude-flow@v3alpha daemon start

# Spawn an agent
npx claude-flow@v3alpha agent spawn -t coder --name my-coder

# Initialize swarm
npx claude-flow@v3alpha swarm init --topology hierarchical --max-agents 8 --strategy specialized

# Run system check
npx claude-flow@v3alpha doctor --fix
```

---

## ⚙️ 技术栈

- TypeScript
- Node.js
- Commander.js (CLI framework)
- AgentDB (HNSW vector search)
- RuVector (Neural intelligence)
- MCP Protocol
- SONA / MoE / EWC++