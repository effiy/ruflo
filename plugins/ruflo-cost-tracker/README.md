---
title: "ruflo-cost-tracker — Token Usage Tracking & Budget Alerts"
tags: ["cost", "budget", "tokens", "tracking", "optimization", "ledger", "pricing"]
category: "plugins/ruflo-cost-tracker"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# ruflo-cost-tracker

Token usage tracking, model cost attribution per agent, budget alerts, and optimization recommendations.

---

## 💡 概述

Tracks token usage per agent, task, and model, then computes USD cost attribution using current model pricing. Monitors configurable budgets with tiered alerts (info at 50%, warning at 75%, critical at 90%, hard stop at 100%). Analyzes usage patterns and recommends optimizations.

---

## ⚡ 功能

### Agents

| Agent | Model | Role |
|-------|-------|------|
| `cost-analyst` | haiku | Token usage tracking, USD cost attribution, budget monitoring, optimization recommendations |

### Skills

| Skill | Usage | Description |
|-------|-------|-------------|
| `cost-report` | `/cost-report [--period today]` | Generate a cost report with token usage and USD costs |
| `cost-optimize` | `/cost-optimize` | Analyze usage patterns and recommend optimizations |
| `cost-track` | `/cost-track` | **Auto-capture** per-session token usage |
| `cost-budget-check` | `/cost-budget-check [--period ...]` | Read totals + budget config, emit 50/75/90/100% alert ladder |
| `cost-booster-route` | `/cost-booster-route <task>` | Route tasks via `hooks_route` and report Agent Booster utilization |
| `cost-benchmark` | `/cost-benchmark [--llm] [--anthropic]` | Run the corpus benchmark |
| `cost-trend` | `/cost-trend` | Read all bench runs and surface drift |
| `cost-projection` | `/cost-projection [--window 7d]` | **Forward** spend extrapolation |
| `cost-counterfactual` | `/cost-counterfactual [--since 7d]` | **Comparative** cost analysis vs baseline strategies |
| `cost-burn` | `/cost-burn [--bucket 1d]` | **Trend** burn-rate analysis |
| `cost-anomaly` | `/cost-anomaly [--since 7d]` | **Point-anomaly** MAD-based outlier detection |
| `cost-diff` | `/cost-diff --baseline <path> --current <path>` | **PR regression detection** |
| `cost-session` | `/cost-session [--session-id <id>]` | **Drill-down** per-message cost breakdown |
| `cost-health` | `/cost-health` | **Composite CI gate** — runs budget+burn+anomaly+projection |
| `cost-ledger` | `/cost-ledger [--since 7d] [--provider ...]` | **Multi-provider** spend, tokens and cache hit ratio |
| `cost-advise` | `/cost-advise [--since 7d]` | **Optimisation findings** from your own logs |
| `cost-openrouter` | `/cost-openrouter key\|generation\|credits` | OpenRouter's own billed cost |
| `cost-local` | `/cost-local --tok-per-s <n>` | Cost per 1M tokens on your own hardware |
| `cost-conversation` | `/cost-conversation` | Per-conversation cost view |
| `cost-export` | `/cost-export [--prometheus <path>]` | Emit cost data as Prometheus textfile |
| `cost-federation` | `/cost-federation` | ADR-097 Phase 3 — per-peer federation_spend rolling windows |
| `cost-summary` | `/cost-summary [--format json\|markdown]` | Programmatic dump of all cost data |
| `cost-compact-context` | `/cost-compact-context <query>` | Retrieval-compacted analysis |

### Commands (23 subcommands)

```bash
cost track                                # Auto-capture this session's token usage (producer)
cost report [--period today|week|month]   # Cost report
cost breakdown [--by agent|model|task]    # Detailed breakdown by dimension
cost optimize                             # Analyze usage and suggest savings
cost outcome <task> <model> <outcome>     # Emit hooks_model-outcome
cost budget set <amount>                  # Set USD budget
cost budget get                           # Show current budget config
cost budget check [--period ...]          # Compute utilization + alert
cost benchmark [--llm] [--anthropic]      # Run measured benchmark
cost trend                                # Drift across bench runs
cost projection [--window 7d]             # Forward USD/day extrapolation
cost counterfactual [--since 7d]          # Multi-baseline comparison
cost burn [--bucket 1d] [--alert ...]     # Burn-rate trend + acceleration alert
cost anomaly [--since 7d] [--alert ...]   # MAD-based per-session outlier detection
cost diff --baseline <p> --current <p>    # Snapshot delta
cost session [--session-id <id>] [--top N] # Per-message drill-down
cost health [--alert-acceleration N] ...  # Composite CI gate
cost conversation                         # Per-conversation cost view
cost summary [--format json|markdown]     # Programmatic JSON contract
cost export [--prometheus] [--webhook]    # External observability
cost federation                           # ADR-097 Phase 3 federation_spend consumer
cost workers                              # Inspect optimize + benchmark loop-workers
cost history                              # Show cost tracking over time
```

---

## 📊 指标

### Model Pricing (per 1M tokens)

| Model | Input | Output | Cache Write (5m) | Cache Read |
|-------|-------|--------|-------------|------------|
| Haiku 4.5 | $1.00 | $5.00 | $1.25 | $0.10 |
| Sonnet 5.5 | $2.00 | $10.00 | $2.50 | $0.20 |
| Opus 5.5 | $4.00 | $20.00 | $5.00 | $0.20 |

### Budget Alert Thresholds

| Level | Threshold | Action |
|-------|-----------|--------|
| Info | 50% consumed | Log notification |
| Warning | 75% consumed | Display warning, suggest optimizations |
| Critical | 90% consumed | Urgent alert, recommend model downgrades |
| Hard Stop | 100% consumed | Halt non-essential agent spawns |

### Optimization Strategies

| Strategy | Savings | Impact |
|----------|---------|--------|
| Downgrade simple tasks to Haiku | 80-92% | Minimal for low-complexity work |
| Enable prompt caching | 90% on reads | None (same quality) |
| Batch similar operations | 15-25% | Slight latency increase |
| Use Agent Booster (Tier 1) | 100% | Only for simple transforms |
| Shorten system prompts | 10-20% | Requires careful pruning |

---

## 🏛️ 设计

### Optimization integration (ADR-0002)

| Capability | Where | Surfaced by |
|---|---|---|
| **Agent Booster bypass** (Tier 1, $0, WASM) | `hooks_route` | `cost-booster-route` skill |
| **Token optimizer / compact context** | `getTokenOptimizer()` from `@claude-flow/integration` | `cost-compact-context` skill |
| **Model-outcome feedback loop** | `hooks_model-outcome` | `cost-optimize` skill step 8 |
| **Optimize + benchmark loop workers** | `hooks_worker-status` | `cost workers` command |

### Federation budget circuit breaker pairing

Fields on `federation_send` MCP tool:
| Field | Default | Effect |
|---|---|---|
| `maxHops` | `8` | Hard ceiling on recursive delegation |
| `maxTokens` | unbounded | Sigma tokens across the whole hop chain |
| `maxUsd` | unbounded | Sigma USD across hops |
| `hopCount` | `0` | Pass-through for re-forwarded messages |
| `spent.{tokens,usd}` | `0` | Caller-reported usage from previous legs |

### Namespace coordination

This plugin owns two AgentDB namespaces:
- `cost-tracking` — usage records (consumed by `cost-report`)
- `cost-patterns` — optimization recommendations (consumed by `cost-optimize`)

---

## 📁 目录

「待补充」

---

## 🔗 资源

- [`ADR-0001` — ruflo-cost-tracker plugin contract](./docs/adrs/0001-cost-tracker-contract.md)
- [`ADR-0002` — agentic-flow + Agent Booster integration](./docs/adrs/0002-agentic-flow-and-agent-booster-integration.md)
- [`ADR-0003` — Implementation arc v0.5 → v0.15](./docs/adrs/0003-implementation-arc-v0.5-to-v0.15.md)
- [`ADR-437` — Cost across providers](../../v3/docs/adr/ADR-437-console-cost-across-providers.md)
- Related: `ruflo-agentdb`, `ruflo-observability`, `ruflo-neural-trader`, `ruflo-federation`

---

## 🚀 快速开始

### Install

```bash
claude --plugin-dir plugins/ruflo-cost-tracker
```

### Verification

```bash
bash plugins/ruflo-cost-tracker/scripts/smoke.sh
# Expected: "63 passed, 0 failed"
```

### Compatibility

- **CLI:** pinned to `@claude-flow/cli` v3.6 major+minor.
- **Verification:** `bash plugins/ruflo-cost-tracker/scripts/smoke.sh` is the contract.

---

## ⚙️ 技术栈

- Token tracking via jsonl parsing
- USD cost computation (model pricing tables)
- Agent Booster (WASM, Tier 1, $0)
- Prometheus textfile export
- OpenRouter API