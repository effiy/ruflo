---
title: "ruflo-daa — Dynamic Agentic Architecture"
tags: ["daa", "cognitive-patterns", "adaptive-agents", "knowledge-sharing", "intelligence", "mcp"]
category: "plugins/ruflo-daa"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# ruflo-daa

Dynamic Agentic Architecture with cognitive patterns, knowledge sharing, and adaptive agents.

---

## 💡 概述

DAA cognitive patterns feed the **JUDGE** phase of the 4-step intelligence pipeline (RETRIEVE → JUDGE → DISTILL → CONSOLIDATE) defined by [ruflo-intelligence ADR-0001](../ruflo-intelligence/docs/adrs/0001-intelligence-surface-completeness.md).

---

## ⚡ 功能

- **Adaptive agents**: Agents that learn and evolve from interactions
- **Cognitive patterns**: Structured reasoning strategies for decision-making
- **Knowledge sharing**: Cross-agent learning and collective improvement
- **Performance tracking**: Efficiency and accuracy metrics over time

### Commands

- `/daa` -- DAA dashboard with learning metrics and active agents

### Skills

- `daa-agent` -- Create and adapt Dynamic Agentic Architecture agents
- `cognitive-pattern` -- Define cognitive patterns for agent reasoning

### MCP surface (8 tools)

| Tool | Purpose |
|------|---------|
| `daa_agent_create` | Initialize an adaptive agent |
| `daa_agent_adapt` | Trigger manual adaptation from feedback |
| `daa_workflow_create` | Define a cognitive workflow |
| `daa_workflow_execute` | Run a cognitive workflow |
| `daa_knowledge_share` | Propagate learnings across agents |
| `daa_learning_status` | Adaptation progress metrics |
| `daa_cognitive_pattern` | Define a reasoning pattern |
| `daa_performance_metrics` | Efficiency / accuracy stats |

---

## 📊 指标

「待补充」

---

## 🏛️ 设计

### Intelligence-pipeline alignment

When a task is routed via `hooks_route` and a similar past trajectory is retrieved, DAA cognitive patterns provide the structured reasoning to evaluate fit before adaptation. `daa_knowledge_share` writes pattern propagation events that `hooks_intelligence_learn` can consume during the DISTILL phase.

### Namespace coordination

This plugin owns the `daa-patterns` AgentDB namespace (kebab-case, follows the convention from [ruflo-agentdb ADR-0001](../ruflo-agentdb/docs/adrs/0001-agentdb-optimization.md)). Reserved namespaces (`pattern`, `claude-memories`, `default`) MUST NOT be shadowed.

### As a mod

Function-hook mod (ADR-445 pattern):
- **Guard** (default on): refuses `daa_*` tool calls that hold a key, token or password.
- **`/daa-mod`**: answered locally — `status`, `scan <text>`, `tools`.
- **Status file**: `.claude-flow/daa-mod/status.json`

---

## 📁 目录

「待补充」

---

## 🔗 资源

- [`ADR-0001` — ruflo-daa plugin contract](./docs/adrs/0001-daa-contract.md)
- [ruflo-intelligence ADR-0001](../ruflo-intelligence/docs/adrs/0001-intelligence-surface-completeness.md)

---

## 🚀 快速开始

### Install

```
/plugin marketplace add ruvnet/ruflo
/plugin install ruflo-daa@ruflo
```

### Verification

```bash
bash plugins/ruflo-daa/scripts/smoke.sh
# Expected: "13 passed, 0 failed"
```

### Compatibility

- **CLI:** pinned to `@claude-flow/cli` v3.6 major+minor.
- **Verification:** `bash plugins/ruflo-daa/scripts/smoke.sh` is the contract.

---

## ⚙️ 技术栈

- MCP protocol (8 tools via `daa-tools.ts`)
- AgentDB (daa-patterns namespace)
- Intelligence pipeline (JUDGE phase)
- Function-hook mod (ADR-445 pattern)
- Claude Code 2.1.287+