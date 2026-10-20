---
title: "ruflo-market-data"
tags: ["market-data", "ohlcv", "candlestick", "patterns", "ingestion", "vectorization"]
category: "plugins/ruflo-market-data"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# ruflo-market-data

Market data ingestion -- feed normalization, OHLCV vectorization, and candlestick pattern detection (read-only analysis, no order placement).

---

## 💡 概述

Ingests market data from REST APIs and WebSocket feeds, normalizes to OHLCV vectors with consistent scaling, and vectorizes candlestick patterns for similarity search.

---

## ⚡ 功能

### Agents

| Agent | Model | Role |
|-------|-------|------|
| `data-engineer` | sonnet | Ingest market feeds, normalize OHLCV, vectorize candlestick patterns, pattern matching |

### Skills

| Skill | Usage | Description |
|-------|-------|-------------|
| `market-ingest` | `/market-ingest <symbol> [--source api]` | Ingest and normalize market data into OHLCV vectors |
| `market-pattern` | `/market-pattern <symbol> [--period 1D]` | Detect and classify candlestick patterns |

### Commands (5 subcommands)

```bash
market ingest <symbol> [--period 1D]    # Ingest and normalize OHLCV data
market patterns <symbol>                 # Detect candlestick patterns
market search <pattern-name>             # Search historical pattern occurrences
market history <symbol>                  # Show ingestion history and data coverage
market compare <sym1> <sym2>             # Compare pattern profiles between symbols
```

---

## 📊 指标

### Pattern Library

| Pattern | Type | Candles | Reliability |
|---------|------|---------|-------------|
| Doji | Reversal | 1 | Medium |
| Hammer | Reversal | 1 | Medium-High |
| Engulfing | Reversal | 2 | High |
| Morning/Evening Star | Reversal | 3 | High |
| Three White Soldiers | Continuation | 3 | High |
| Head & Shoulders | Reversal | 5-7 | Very High |
| Double Top/Bottom | Reversal | Variable | High |

Each pattern is encoded as a 64-dimension padded vector.

---

## 🏛️ 设计

### OHLCV Normalization

| Field | Normalization |
|-------|--------------|
| Open | Relative to previous close: `(open - prev_close) / prev_close` |
| High | Relative to open: `(high - open) / open` |
| Low | Relative to open: `(low - open) / open` |
| Close | Relative to open: `(close - open) / open` |
| Volume | Z-score: `(vol - mean_vol) / std_vol` |

### Namespace coordination

This plugin owns two AgentDB namespaces:
- `market-data` — normalized OHLCV vectors per symbol+date
- `market-patterns` — detected candlestick patterns with reliability scores

### As a mod

Function-hook mod (ADR-445). **Guard** refuses writes to `market*` namespace that hold credentials. **Status file**: `.claude-flow/market-mod/status.json`. Options: `guard` (on).

---

## 📁 目录

「待补充」

---

## 🔗 资源

- [`ADR-0001` — ruflo-market-data plugin contract](./docs/adrs/0001-market-data-contract.md)
- Related: `ruflo-agentdb`, `ruflo-neural-trader`, `ruflo-ruvector`, `ruflo-observability`

---

## 🚀 快速开始

### Install

```bash
claude --plugin-dir plugins/ruflo-market-data
```

### Verification

```bash
bash plugins/ruflo-market-data/scripts/smoke.sh
# Expected: "11 passed, 0 failed"
```

### Compatibility

- **CLI:** pinned to `@claude-flow/cli` v3.6 major+minor.
- **Verification:** `bash plugins/ruflo-market-data/scripts/smoke.sh` is the contract.

---

## ⚙️ 技术栈

- REST APIs / WebSocket feeds
- OHLCV vector encoding (64-dim)
- AgentDB namespaces
- Function-hook mod (ADR-445)