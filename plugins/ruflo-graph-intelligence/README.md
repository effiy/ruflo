---
title: "ruflo-graph-intelligence"
tags: ["graph", "pagerank", "streaming", "witness", "plugin"]
category: "plugins"
created: "2024-01-01"
updated: "2026-10-20"
source: "internal"
type: "index"
status: "stable"
lifecycle: "active"
---

# ruflo-graph-intelligence

## 💡 概述

Graph Intelligence Engine for RuFlo: single-entry personalized PageRank, streaming delta updates and witness-signed reasoning artifacts (ADR-123), built on sublinear-time-solver. Library: `npm test`, `npm run build`.

---

## ⚡ 功能

### As a mod (0.2.0-alpha.6)

A function-hook mod ships beside the skills (ADR-445 pattern). Needs a Claude Code with mods (2.1.287+); older builds ignore it. No network, no process spawning: it only tightens calls to this plugin's own tools and reads through tools already connected.

| Piece | Default | What it does |
|---|---|---|
| **Write guard** | on | Refuses a call to a `sublinear/*` (`sublinear_*`) tool whose input holds a key, token or password: graph, node and seed ids land in signed artifacts that federation can copy. |
| **`/graph-mod`** | -- | `status`, `scan <text>`, `tools` (lists connected graph-intelligence tools); answered locally, no model call. |
| **Status file** | -- | `.claude-flow/graph-mod/status.json` (`version`, `updatedMs`, mode flags and counters); written at session start and when a counter changes. |

Options (`userConfig`): `guard` on|off. Refusals never echo the value they matched.

---

## 📊 指标

「待补充」

---

## 🏛️ 设计

「待补充」

---

## 📁 目录

```
plugins/ruflo-graph-intelligence/
├── hooks/
├── scripts/
│   └── smoke.sh
└── README.md
```

---

## 🔗 资源

「待补充」

---

## 🚀 快速开始

```bash
bash plugins/ruflo-graph-intelligence/scripts/smoke.sh   # stages the mod and runs claude plugin test (7 tests); the engine's own tests: npm test
```

---

## ⚙️ 技术栈

- sublinear-time-solver
- ADR-123 witness-signed reasoning artifacts
- ADR-445 function-hook mod
- Claude Code 2.1.287+