---
title: "ruflo-plugin-creator"
tags: ["plugin", "scaffold", "claude-code", "validation", "mcp", "contract"]
category: "plugins/ruflo-plugin-creator"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# ruflo-plugin-creator

Scaffold, validate, and publish new Claude Code plugins with proper structure, MCP tool wiring, AND the canonical plugin contract (ADR + smoke + Compatibility + namespace coordination).

---

## 💡 概述

A scaffolding tool for creating Claude Code plugins that enforces the canonical contract every other plugin in the ruflo family adopted via ADR-0001. Includes scaffolding, validation, MCP tool wiring with drift warnings, and marketplace integration.

---

## ⚡ 功能

- **Scaffold plugins**: Generate complete plugin directory structure in seconds, including the canonical contract (ADR-0001, scripts/smoke.sh, README contract sections)
- **Validate format**: Check plugin.json, SKILL.md frontmatter, and file references
- **MCP tool wiring**: Auto-discover and wire ruflo MCP tools into skills, with **drift warnings** for known-broken tool references
- **Marketplace integration**: Update marketplace.json for distribution

## Commands

- `/create-plugin` -- Interactively scaffold a new Claude Code plugin

## Skills

- `create-plugin` -- Generate plugin structure with skills, commands, agents, ADR-0001, smoke test, and contract sections
- `validate-plugin` -- Validate plugin format and catch issues before publishing
- `create-mod` -- Scaffold a Claude Code mod (function hooks) from `templates/mod/`

---

## 📊 指标

「待补充」

---

## 🏛️ 设计

### Canonical plugin contract

```
plugins/<name>/
├── .claude-plugin/plugin.json     # version, keywords, mcp keyword
├── skills/<skill>/SKILL.md         # name + description + allowed-tools (no wildcards)
├── commands/<command>.md           # name + description + dispatch logic
├── agents/<agent>.md               # name + description + model
├── docs/adrs/0001-<name>-contract.md   # ADR (Proposed) — pinning, namespace, smoke scope
├── scripts/smoke.sh                # Structural contract, >=8 checks
└── README.md                       # Compatibility + Namespace coordination + Verification + ADR
```

### MCP-tool drift to avoid

| Bug | Real tool / pattern |
|-----|---------------------|
| `embeddings_embed` referenced as a tool | Use `embeddings_generate` |
| `namespace` arg passed to `agentdb_hierarchical-*` | Use `tier` (working/episodic/semantic), or use `memory_*` for namespaced reads/writes |
| `namespace` arg passed to `agentdb_pattern-*` | Don't pass it — ReasoningBank routes; fallback writes to `pattern` reserved |
| `pattern` and `patterns` confused | They are **different** reserved namespaces |
| Hard-coded "19 AgentDB controllers" | Defer to `agentdb_controllers` runtime; real count varies |

### As a mod

Function-hook mod (ADR-445, pattern of `ruflo-agentdb`), loaded from `hooks/hooks.json` → `hooks/register.ts`.

- **Command**: `/creator-mod` answers locally with no model call, read-only
- **Status file**: `.claude-flow/creator-mod/status.json`
- **Safety**: no network, no process spawning

---

## 📁 目录

「待补充」

---

## 🔗 资源

- [`ADR-0001` — ruflo-plugin-creator plugin contract](./docs/adrs/0001-plugin-creator-contract.md)
- Related: `ruflo-agentdb`, `ruflo-cost-tracker`, `ruflo-market-data`, `ruflo-migrations`, `ruflo-observability`

---

## 🚀 快速开始

### Install

```
/plugin marketplace add ruvnet/ruflo
/plugin install ruflo-plugin-creator@ruflo
```

### Verification

```bash
bash plugins/ruflo-plugin-creator/scripts/smoke.sh
# Expected: "11 passed, 0 failed"
```

### Compatibility

- **CLI:** pinned to `@claude-flow/cli` v3.6 major+minor.
- **Verification:** `bash plugins/ruflo-plugin-creator/scripts/smoke.sh` is the contract.

---

## ⚙️ 技术栈

- Claude Code plugin system
- Function-hook mod (ADR-445 pattern)
- TypeScript (mod scaffolding)