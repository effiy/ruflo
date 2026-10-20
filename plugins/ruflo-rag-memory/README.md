---
title: "ruflo-rag-memory — RAG Memory with HNSW Vector Search"
tags: ["rag", "memory", "hnsw", "agentdb", "vector-search", "onnx", "embeddings", "smart-retrieval"]
category: "plugins/ruflo-rag-memory"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# ruflo-rag-memory

Retrieval-Augmented Generation memory with HNSW vector search, AgentDB persistence, and Claude Code memory bridge.

---

## 💡 概述

Provides semantic store/search/recall over AgentDB with HNSW-indexed vector search (measured ~1.9x at N=20k, ~3.2x–4.7x at N=5k vs brute force, recall@10 ~0.99; ANN wins above the index-size crossover). Bridges Claude Code's native auto-memory into AgentDB with 384-dim ONNX embeddings for unified cross-session semantic retrieval.

---

## ⚡ 功能

### Agents

| Agent | Model | Role |
|-------|-------|------|
| `memory-specialist` | sonnet | AgentDB management, HNSW optimization, memory bridge, consolidation |

### Skills

| Skill | Usage | Description |
|-------|-------|-------------|
| `memory-search` | `/memory-search <query>` | Semantic vector search across all namespaces |
| `memory-bridge` | `/memory-bridge [--all-projects]` | Import Claude Code auto-memory into AgentDB |

### Commands

```bash
memory store --key "pattern-auth" --value "JWT with refresh tokens" --namespace patterns
memory search --query "authentication patterns" --namespace patterns --limit 5
memory retrieve --key "pattern-auth" --namespace patterns
memory list --namespace patterns --limit 10
memory delete --key "old-entry" --namespace patterns
/recall how did we handle rate limiting?
```

### SmartRetrieval (ADR-090)

5-phase retrieval pipeline for higher-quality recall across sessions:
1. **Query expansion** -- template-based variant generation (no LLM)
2. **Multi-query fan-out + RRF** -- Reciprocal Rank Fusion across variants
3. **Recency boost** -- exponential decay from metadata timestamps
4. **MMR diversity** -- token-Jaccard Maximal Marginal Relevance re-ranking
5. **Session round-robin** -- interleaved results from distinct sessions

### Claude Memory Bridge

Auto-imports Claude Code's native `~/.claude/projects/*/memory/*.md` files into AgentDB on session start with ONNX vector embeddings.

### Memory Namespaces

| Namespace | Purpose | Example Key |
|-----------|---------|-------------|
| `patterns` | Successful code/design patterns | `pattern-auth-jwt` |
| `tasks` | Task context and outcomes | `task-refactor-api` |
| `solutions` | Bug fixes and solutions | `fix-race-condition` |
| `feedback` | User feedback and corrections | `feedback-test-style` |
| `security` | Vulnerability patterns | `vuln-sql-injection` |
| `claude-memories` | Bridged Claude Code memories | `auto-imported` |

---

## 📊 指标

### HNSW Performance

| Operation | vs Brute Force | Notes |
|-----------|----------------|-------|
| Vector search (N=5k) | ~3.2x–4.7x faster | ruvector NAPI, recall@10 ~0.99 |
| Vector search (N=20k) | ~1.9x faster | ANN wins above crossover |
| Vector search (below crossover) | ties/loses | brute force preferred for small N |

The previously published "150x–12,500x" figures were brute-force fallback artifacts and are not reproduced under the audit harness.

---

## 🏛️ 设计

### Architecture

```
Claude Code Auto-Memory (~/.claude/projects/*/memory/*.md)
        │
        ▼ (ONNX all-MiniLM-L6-v2, 384-dim)
    Memory Bridge
        │
        ▼
    AgentDB (SQLite + vector_indexes)
        │
        ├── patterns namespace
        ├── tasks namespace
        ├── solutions namespace
        ├── feedback namespace
        ├── security namespace
        └── claude-memories namespace
        │
        ▼ (HNSW ANN index)
    Semantic Search (HNSW ANN)
```

### Encryption at rest (ruflo 3.6.25+)

Opt-in AES-256-GCM encryption at rest per [ADR-096](../../v3/docs/adr/ADR-096-encryption-at-rest.md). When `CLAUDE_FLOW_ENCRYPT_AT_REST=1` and `CLAUDE_FLOW_ENCRYPTION_KEY` is set, writes are encrypted with fresh 12-byte IV and reads sniff `RFE1` magic byte for legacy compat.

### As a mod

Function-hook mod (ADR-445). A write guard for memory: `memory_store`, `agentdb_hierarchical-store` and `agentdb_pattern-store` refuse secrets. Command: `/rag-mod`. Status file: `.claude-flow/rag-mod/status.json`.

---

## 📁 目录

「待补充」

---

## 🔗 资源

- [`ADR-0001` — ruflo-rag-memory plugin contract](./docs/adrs/0001-rag-memory-contract.md)
- Related: `ruflo-agentdb`, `ruflo-ruvector`, `ruflo-rvf`, `ruflo-knowledge-graph`

---

## 🚀 快速开始

```bash
# Store a pattern
npx ruflo memory store --key "oauth-flow" --value "OAuth2 with pkce for SPAs" --namespace patterns

# Search for it later
npx ruflo memory search -q "oauth single page app"

# Retrieve exact entry
npx ruflo memory retrieve --key "oauth-flow" --namespace patterns
```

### Install

```bash
claude --plugin-dir plugins/ruflo-rag-memory
```

### Verification

```bash
bash plugins/ruflo-rag-memory/scripts/smoke.sh
# Expected: "10 passed, 0 failed"
```

### Compatibility

- **CLI:** pinned to `@claude-flow/cli` v3.6 major+minor.
- **Requires:** `ruflo-core` plugin (provides MCP server)

---

## ⚙️ 技术栈

- AgentDB (SQLite + vector_indexes)
- HNSW vector indexing (ruvector NAPI)
- ONNX embeddings (all-MiniLM-L6-v2, 384-dim)
- AES-256-GCM encryption (opt-in)
- MMR diversity re-ranking
- Reciprocal Rank Fusion