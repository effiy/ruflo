---
title: "V3 Architecture Decision Records (ADRs)"
tags: ["ADR", "architecture", "decisions", "DDD", "MCP", "plugin", "memory", "security"]
category: "implementation/adrs"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# V3 Architecture Decision Records (ADRs)

This directory contains all Architecture Decision Records for Claude-Flow v3.

**Status:** ✅ **BETA READY** (22 ADRs Complete)
**Version:** 3.0.0-alpha.84
**Last Updated:** 2026-01-13

---

## 💡 概述

### Quick Summary

#### Core Decisions

1. **ADR-001**: Build on agentic-flow@alpha instead of duplicating (eliminates 10,000+ lines)
2. **ADR-002**: Domain-Driven Design with bounded contexts for clean architecture
3. **ADR-003**: Single UnifiedSwarmCoordinator as canonical coordination engine
4. **ADR-004**: Microkernel with plugins for optional features (HiveMind, Neural, etc.)
5. **ADR-005**: MCP tools as primary API, CLI as thin wrapper

#### Technical Decisions

6. **ADR-006**: Single MemoryService with SQLite, AgentDB, or Hybrid backends
7. **ADR-007**: Event sourcing for audit trail and state reconstruction
8. **ADR-008**: Vitest for 10x faster testing with native ESM
9. **ADR-009**: Hybrid backend (SQLite + AgentDB) as default for best performance
10. **ADR-010**: Node.js 20+ only, removing Deno complexity

---

## ⚡ 功能

### ADR Index

#### Core Architecture (ADR-001 to ADR-010)

| ADR | Title | Status | File |
|-----|-------|--------|------|
| ADR-001 | Adopt agentic-flow as Core Foundation | ✅ Implemented | [ADR-001-AGENT-IMPLEMENTATION.md](./ADR-001-AGENT-IMPLEMENTATION.md) |
| ADR-002 | Implement Domain-Driven Design Structure | ✅ Implemented | [ADR-002-DDD-STRUCTURE.md](./ADR-002-DDD-STRUCTURE.md) |
| ADR-003 | Single Coordination Engine | ✅ Implemented | [ADR-003-CONSOLIDATION-COMPLETE.md](./ADR-003-CONSOLIDATION-COMPLETE.md) |
| ADR-004 | Plugin-Based Architecture | ✅ Implemented | [ADR-004-PLUGIN-ARCHITECTURE.md](./ADR-004-PLUGIN-ARCHITECTURE.md) |
| ADR-005 | MCP-First API Design | ✅ Implemented | [ADR-005-implementation-summary.md](./ADR-005-implementation-summary.md) |
| ADR-006 | Unified Memory Service | ✅ Implemented | [ADR-006-UNIFIED-MEMORY.md](./ADR-006-UNIFIED-MEMORY.md) |
| ADR-007 | Event Sourcing for State Changes | ✅ Implemented | [ADR-007-EVENT-SOURCING.md](./ADR-007-EVENT-SOURCING.md) |
| ADR-008 | Vitest Over Jest | ✅ Implemented | [ADR-008-VITEST.md](./ADR-008-VITEST.md) |
| ADR-009 | Hybrid Memory Backend as Default | ✅ Implemented | [ADR-009-IMPLEMENTATION.md](./ADR-009-IMPLEMENTATION.md) |
| ADR-010 | Remove Deno Support | ✅ Implemented | [ADR-010-NODE-ONLY.md](./ADR-010-NODE-ONLY.md) |

#### Providers & Security (ADR-011 to ADR-017)

| ADR | Title | Status | File |
|-----|-------|--------|------|
| ADR-011 | LLM Provider System | ✅ Implemented | [ADR-011-llm-provider-system.md](./ADR-011-llm-provider-system.md) |
| ADR-012 | MCP Security Features | ✅ Implemented | [ADR-012-mcp-security-features.md](./ADR-012-mcp-security-features.md) |
| ADR-013 | Core Security Module | ✅ Implemented | [ADR-013-core-security-module.md](./ADR-013-core-security-module.md) |
| ADR-014 | Workers System | ✅ Implemented | [ADR-014-workers-system.md](./ADR-014-workers-system.md) |
| ADR-015 | Unified Plugin System | ✅ Implemented | [ADR-015-unified-plugin-system.md](./ADR-015-unified-plugin-system.md) |
| ADR-016 | Collaborative Issue Claims | ✅ Implemented | [ADR-016-collaborative-issue-claims.md](./ADR-016-collaborative-issue-claims.md) |
| ADR-017 | RuVector Integration | ✅ Implemented | [ADR-017-ruvector-integration.md](./ADR-017-ruvector-integration.md) |

#### Advanced Features (ADR-018 to ADR-025)

| ADR | Title | Status | File |
|-----|-------|--------|------|
| ADR-018 | Claude Code Integration | ✅ Implemented | [ADR-018-claude-code-integration.md](./ADR-018-claude-code-integration.md) |
| ADR-019 | Headless Runtime Package | ✅ Implemented | [ADR-019-headless-runtime-package.md](./ADR-019-headless-runtime-package.md) |
| ADR-020 | Headless Worker Integration | ✅ Implemented | [ADR-020-headless-worker-integration.md](./ADR-020-headless-worker-integration.md) |
| ADR-021 | Transfer Hook IPFS Pattern Sharing | ✅ Implemented | [ADR-021-transfer-hook-ipfs-pattern-sharing.md](./ADR-021-transfer-hook-ipfs-pattern-sharing.md) |
| ADR-022 | AIDefence Integration | ✅ Implemented | [ADR-022-aidefence-integration.md](./ADR-022-aidefence-integration.md) |
| ADR-023 | ONNX Hyperbolic Embeddings Init | ✅ Implemented | [ADR-023-onnx-hyperbolic-embeddings-init.md](./ADR-023-onnx-hyperbolic-embeddings-init.md) |
| ADR-024 | Embeddings MCP Tools | ✅ Implemented | [ADR-024-embeddings-mcp-tools.md](./ADR-024-embeddings-mcp-tools.md) |
| ADR-025 | Auto-Update System | ✅ Implemented | [ADR-025-auto-update-system.md](./ADR-025-auto-update-system.md) |

#### Neural & Integration (ADR-026 to ADR-031)

| ADR | Title | Status | File |
|-----|-------|--------|------|
| ADR-026 | Agent Booster Model Routing | ✅ Implemented | [ADR-026-agent-booster-model-routing.md](./ADR-026-agent-booster-model-routing.md) |
| ADR-027 | RuVector PostgreSQL Integration | ✅ Implemented | [ADR-027-ruvector-postgresql-integration.md](./ADR-027-ruvector-postgresql-integration.md) |
| ADR-028 | Neural Attention Mechanisms | ✅ Implemented | [ADR-028-neural-attention-mechanisms.md](./ADR-028-neural-attention-mechanisms.md) |
| ADR-029 | GNN Integration | ✅ Implemented | [ADR-029-gnn-integration.md](./ADR-029-gnn-integration.md) |
| ADR-030 | Agentic QE Integration | ⛔ Superseded (in-repo plugin removed; use the `agentic-qe` package) | [ADR-030-agentic-qe-integration.md](./ADR-030-agentic-qe-integration.md) |
| ADR-031 | Prime Radiant Integration | ✅ Implemented | [ADR-031-prime-radiant-integration.md](./ADR-031-prime-radiant-integration.md) |

#### Packaging & Branding (ADR-042 to ADR-048)

| ADR | Title | Status | File |
|-----|-------|--------|------|
| ADR-046 | Dual Umbrella: claude-flow + ruflo | Accepted | [ADR-046-ruflo-rebrand.md](./ADR-046-ruflo-rebrand.md) |
| ADR-047 | Fast Mode Integration | Proposed | [ADR-047-fast-mode-integration.md](./ADR-047-fast-mode-integration.md) |
| ADR-048 | Auto Memory Integration | Accepted | [ADR-048-auto-memory-integration.md](./ADR-048-auto-memory-integration.md) |

#### RuVector WASM Plugin Architecture (ADR-032 to ADR-041)

| ADR | Title | Category | Status | File |
|-----|-------|----------|--------|------|
| ADR-032 | Healthcare Clinical Decision Support | Practical | Proposed | [ADR-032-healthcare-clinical-plugin.md](./ADR-032-healthcare-clinical-plugin.md) |
| ADR-033 | Financial Risk Analysis | Practical | Proposed | [ADR-033-financial-risk-plugin.md](./ADR-033-financial-risk-plugin.md) |
| ADR-034 | Legal Contract Analysis | Practical | Proposed | [ADR-034-legal-contract-plugin.md](./ADR-034-legal-contract-plugin.md) |
| ADR-035 | Advanced Code Intelligence | Advanced | Proposed | [ADR-035-code-intelligence-plugin.md](./ADR-035-code-intelligence-plugin.md) |
| ADR-036 | Test Intelligence | Advanced | Proposed | [ADR-036-test-intelligence-plugin.md](./ADR-036-test-intelligence-plugin.md) |
| ADR-037 | Performance Optimization | Advanced | Proposed | [ADR-037-performance-optimization-plugin.md](./ADR-037-performance-optimization-plugin.md) |
| ADR-038 | Multi-Agent Neural Coordination | Cutting-Edge | Proposed | [ADR-038-multi-agent-coordination-plugin.md](./ADR-038-multi-agent-coordination-plugin.md) |
| ADR-039 | Cognitive Kernel | Cutting-Edge | Proposed | [ADR-039-cognitive-kernel-plugin.md](./ADR-039-cognitive-kernel-plugin.md) |
| ADR-040 | Quantum-Inspired Optimization | Exotic SOTA | Proposed | [ADR-040-quantum-inspired-plugin.md](./ADR-040-quantum-inspired-plugin.md) |
| ADR-041 | Hyperbolic Reasoning | Exotic SOTA | Proposed | [ADR-041-hyperbolic-reasoning-plugin.md](./ADR-041-hyperbolic-reasoning-plugin.md) |

---

## 📊 指标

### Implementation Progress

**Last Updated:** 2026-01-13 (Beta Ready Audit)
**Status:** ✅ **READY FOR 3.0.0-beta.1**

| Component | Status | Details |
|-----------|--------|---------|
| DDD Modules | ✅ 100% | 16 packages, 419 TS files |
| CLI Commands | ✅ 100% | 28 commands, 140+ subcommands |
| MCP Tools | ✅ 100% | 119 tools (exceeds V2) |
| Hooks System | ✅ 100% | 20 CLI + 60 MCP hook tools |
| Test Coverage | ✅ 85%+ | 85+ test files (ADR-008) |
| Service Integration | ✅ Complete | agentic-flow@alpha integration |
| RuVector Integration | ✅ Complete | Q-Learning, AST, Diff, Coverage (ADR-017) |
| Performance Benchmarks | ✅ Complete | Full benchmark suite |
| Auto-Update System | ✅ Complete | Rate-limited startup checks (ADR-025) |
| Production Hardening | ✅ Complete | Real metrics, labeled examples, fallback warnings |

**Overall V3 Implementation: 100% Complete**

### Beta Readiness Checklist

| Category | Status |
|----------|--------|
| Real ONNX embeddings | ✅ |
| Real performance metrics | ✅ |
| Real security scanning | ✅ |
| Fallback warnings | ✅ |
| Auto-update system | ✅ |
| Claims MCP tools | ✅ |
| Production hardening | ✅ |
| Windows validated | ✅ |

### Performance Targets (from ADRs)

| Metric | Target | ADR Reference |
|--------|--------|---------------|
| Code reduction | <5,000 lines vs 15,000+ | ADR-001 |
| HNSW search | 150x-12,500x faster | ADR-009 |
| Flash Attention | 2.49x-7.47x speedup | ADR-001 |
| Test execution | <5s (10x improvement) | ADR-008 |
| Startup time | <500ms | ADR-004 |
| Query latency | <100ms | ADR-006 |

---

## 🏛️ 设计

### Security Improvements

All ADRs consider security:
- CVE-1: Command injection prevention (ADR-005 input validation)
- CVE-2: Path traversal prevention (ADR-006 memory sandboxing)
- CVE-3: Credential generation (secure random with rejection sampling)

---

## 📁 目录

### Additional Files

- [v3-adrs.md](./v3-adrs.md) - Complete ADR master document with all decisions
- [ADR-003-implementation-status.md](./ADR-003-implementation-status.md) - Detailed implementation tracking

---

## 🔗 资源

「待补充」

---

## 🚀 快速开始

### agentic-flow vs claude-flow Feature Comparison

#### Feature Matrix

| Feature | agentic-flow | claude-flow | Integration Value |
|---------|--------------|-------------|-------------------|
| **Core Agent System** | | | |
| Specialized Agents | 66+ | 15 (hierarchical) | 🟡 |
| Agent Spawning | ✅ | ✅ | - |
| Agent Pool/Scaling | ✅ | ✅ | - |
| **Orchestration** | | | |
| Swarm Coordination | ✅ | ✅ | - |
| Hive-Mind Consensus | ❌ | ✅ | - |
| Federation Hub | ✅ | ❌ | 🔴 High |
| Ephemeral Agents | ✅ | ❌ | 🔴 High |
| **Transport** | | | |
| HTTP/SSE | ✅ | ✅ | - |
| QUIC (UDP) | ✅ | ❌ | 🔴 High |
| WebSocket | ✅ | ✅ | - |
| **AI Providers** | | | |
| Multi-Provider | ✅ | ✅ | - |
| Model Optimization | ✅ | ❌ | 🔴 High |
| Provider Fallback | ✅ | ❌ | 🔴 High |
| Cost Tracking | ✅ | ❌ | 🟡 Medium |
| Proxy Server | ✅ | ❌ | 🟡 Medium |
| **Memory/Learning** | | | |
| ReasoningBank (WASM) | ✅ | ❌ | 🔴 High |
| Embeddings CLI | ✅ | ✅ | - |
| HNSW Indexing | ✅ | ✅ | - |
| Memory Persistence | ✅ | ✅ | - |
| **DevOps** | | | |
| Doctor Command | ❌ | ✅ | - |
| Shell Completions | ❌ | ✅ | - |
| Smart Errors | ❌ | ✅ | - |
| Background Workers | ✅ | ✅ | - |
| Worker Dispatch | ✅ | ❌ | 🟡 Medium |
| **Security** | | | |
| Security Scanning | ❌ | ✅ | - |
| CVE Detection | ❌ | ✅ | - |
| Claims/RBAC | ❌ | ✅ | - |
| **Advanced** | | | |
| Neural Patterns | ❌ | ✅ | - |
| Self-Learning Hooks | ✅ | ✅ | - |
| Deployment Mgmt | ❌ | ✅ | - |
| Plugin System | ❌ | ✅ | - |

---

## ⚙️ 技术栈

### CLI MCP Tool Coverage

| Category | Tools | CLI Status |
|----------|-------|------------|
| Agent | spawn, terminate, status, list, pool, health, update | ✅ Complete |
| Hive-Mind | init, spawn, status, task, join, leave, consensus, broadcast, memory, optimize-memory, shutdown | ✅ Complete |
| Task | create, status, list, complete, cancel | ✅ Complete |
| Session | save, restore, list, delete, export | ✅ Complete |
| Config | get, set, list, reset, export, import | ✅ Complete |
| Memory | store, retrieve, list, delete, search | ✅ Complete |
| Workflow | create, execute, list, status, delete | ✅ Complete |

### V3 Advanced CLI Commands

| Command | Description | Subcommands |
|---------|-------------|-------------|
| `neural` | Neural pattern training, MoE, Flash Attention | train, status, patterns, predict, optimize |
| `security` | Security scanning, CVE detection, threat modeling | scan, cve, threats, audit, secrets |
| `performance` | Performance profiling, benchmarking, optimization | benchmark, profile, metrics, optimize, bottleneck |
| `providers` | AI provider management, models, configurations | list, configure, test, models, usage |
| `plugins` | Plugin management, installation, lifecycle | list, install, uninstall, toggle, info, create |
| `deployment` | Deployment management, environments, rollbacks | deploy, status, rollback, history, environments, logs |
| `claims` | Claims-based authorization, access control | list, check, grant, revoke, roles, policies |
| `embeddings` | Vector embeddings, semantic search | generate, search, compare, collections, index, providers |
| `doctor` | System diagnostics | Node version, config, daemon, memory, API keys, MCP, disk |
| `completions` | Shell completions | bash, zsh, fish, powershell |