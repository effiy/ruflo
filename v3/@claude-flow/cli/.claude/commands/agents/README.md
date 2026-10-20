---
title: "Agents Commands"
tags: ["agents", "cli", "claude-flow", "commands", "swarm"]
category: "commands/agents"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# Agents Commands

Complete agent management commands for Claude Flow V3.

---

## 💡 概述

Agent management command set for Claude Flow V3, providing lifecycle management for 60+ agent types across core development, swarm coordination, consensus, GitHub, SPARC, and specialized domains.

---

## ⚡ 功能

### Available Commands

| Command | Description |
|---------|-------------|
| [spawn](./spawn.md) | Spawn new agents with V3 capabilities |
| [list](./list.md) | List all active agents |
| [status](./status.md) | Show detailed agent status |
| [stop](./stop.md) | Stop running agents |
| [metrics](./metrics.md) | View performance metrics |
| [pool](./pool.md) | Manage agent pool scaling |
| [health](./health.md) | Monitor agent health |
| [logs](./logs.md) | View agent activity logs |

### Reference

| Reference | Description |
|-----------|-------------|
| [agent-types](./agent-types.md) | All 87 available agent types |
| [agent-capabilities](./agent-capabilities.md) | Capability matrix by agent |
| [agent-coordination](./agent-coordination.md) | Multi-agent coordination patterns |
| [agent-spawning](./agent-spawning.md) | Best practices for spawning |

### V3 Agent Categories

- **Core**: coder, reviewer, tester, planner, researcher
- **V3 Specialized**: security-architect, memory-specialist, performance-engineer
- **Swarm**: hierarchical-coordinator, mesh-coordinator, adaptive-coordinator
- **Consensus**: byzantine-coordinator, raft-manager, gossip-coordinator
- **GitHub**: pr-manager, code-review-swarm, release-manager
- **SPARC**: sparc-coordinator, specification, architecture, refinement

---

## 📊 指标

「待补充」

---

## 🏛️ 设计

Agents follow a hierarchical/specialized anti-drift pattern with raft consensus for state management. The agent system supports 60+ types organized into 6 categories with clear role boundaries.

---

## 📁 目录

| File | Description |
|------|-------------|
| `spawn.md` | Agent spawn command |
| `list.md` | Agent list command |
| `status.md` | Agent status command |
| `stop.md` | Agent stop command |
| `metrics.md` | Agent metrics command |
| `pool.md` | Agent pool command |
| `health.md` | Agent health command |
| `logs.md` | Agent logs command |
| `agent-types.md` | Agent type reference |
| `agent-capabilities.md` | Capability matrix |
| `agent-coordination.md` | Coordination patterns |
| `agent-spawning.md` | Spawning best practices |

---

## 🔗 资源

「待补充」

---

## 🚀 快速开始

```bash
# Spawn a coder agent
npx @claude-flow/cli@latest agent spawn -t coder --name my-coder

# List all active agents
npx @claude-flow/cli@latest agent list

# Check agent health
npx @claude-flow/cli@latest agent health

# View metrics
npx @claude-flow/cli@latest agent metrics --period 24h
```

---

## ⚙️ 技术栈

- Claude Flow CLI V3
- Raft Consensus
- Hierarchical Topology
- AgentDB Memory Backend