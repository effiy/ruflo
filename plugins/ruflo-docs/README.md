---
title: "ruflo-docs"
tags: ["docs", "documentation", "drift-detection", "api-docs", "auto-documentation"]
category: "plugins"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# ruflo-docs

## 💡 概述

Documentation generation, drift detection, and API docs automation.

## ⚡ 功能

- **Auto-Documentation**: Background worker generates docs from code changes
- **Drift Detection**: Identifies when docs fall out of sync with implementation
- **API Docs**: Automated API documentation from TypeScript interfaces and JSDoc
- **CAPABILITIES.md Generation**: Full capabilities reference via `init` command
- **Document Worker**: Background `document` worker triggers on API changes
- **SPARC Integration**: Uses documenter and docs-writer agent patterns

### Document-worker contract

Drives the `document` background worker (one of 12 workers in CLAUDE.md). Two invocation paths:

```bash
# CLI
npx @claude-flow/cli@latest hooks worker dispatch --trigger document
npx @claude-flow/cli@latest hooks worker dispatch --trigger document --context api

# MCP
mcp tool call hooks_worker-dispatch --json -- '{"trigger": "document", "context": "api"}'
```

| Context | Output |
|-------|--------|
| (none) | Full project documentation pass |
| `api` | API reference from JSDoc/TSDoc + OpenAPI 3.0 for HTTP endpoints |
| `<file-path>` | Single-file doc generation |

---

## 📊 指标

### Requires

- `ruflo-core` plugin (provides MCP server)

### Compatibility

- **CLI:** pinned to `@claude-flow/cli` v3.6 major+minor.
- **Agent model:** Haiku (cost-efficient for docs work).
- **Verification:** `bash plugins/ruflo-docs/scripts/smoke.sh` is the contract.

---

## 🏛️ 设计

### Namespace coordination

This plugin owns the `docs-drift` AgentDB namespace (kebab-case, follows the convention from [ruflo-agentdb ADR-0001 §"Namespace convention"](../ruflo-agentdb/docs/adrs/0001-agentdb-optimization.md)). Used for drift-detection state (last-seen export hash per file). Reserved namespaces (`pattern`, `claude-memories`, `default`) MUST NOT be shadowed.

`docs-drift` is accessed via `memory_*` tools (namespace-routed).

### Architecture Decisions

- [`ADR-0001` -- ruflo-docs plugin contract (document-worker integration, namespace coordination, smoke as contract)](./docs/adrs/0001-docs-contract.md)

### As a mod

Since this version the plugin is also a function-hook mod (ADR-445 pattern; needs Claude Code 2.1.287 or later). It never calls the network or spawns a process, and it only tightens: it can refuse a call, never allow one.

- **Guard** (default on): refuses `doc-*` memory writes and the `document` worker dispatch that hold a key, token or password. The reason names the kind of secret, never the value.
- **`/docs-mod`**: answered locally, no model turn: `status`, `scan <text>` (would the guard refuse this?), and `docs` (markdown files in `docs/`).
- **Status file**: `.claude-flow/docs-mod/status.json` (`{version, updatedMs, guard, blocked}`), written at session start and when a call is blocked.
- **Option**: `guard` (`on` | `off`, default `on`) in the plugin's `userConfig`.

Test it: `claude plugin test plugins/ruflo-docs` and `bash plugins/ruflo-docs/scripts/smoke.sh`.

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Verification

```bash
bash plugins/ruflo-docs/scripts/smoke.sh
# Expected: "13 passed, 0 failed"
```

### Related Plugins

- `ruflo-agentdb` -- namespace convention owner
- `ruflo-loop-workers` -- defines the `document` background worker
- `ruflo-adr` -- ADRs trigger doc generation when status changes
- `ruflo-sparc` -- Documenter mode (Phase 5 Refinement) consumes this plugin

### Install

```
/plugin marketplace add ruvnet/ruflo
/plugin install ruflo-docs@ruflo
```

---

## 🚀 快速开始

```
/plugin install ruflo-docs@ruflo
```

---

## ⚙️ 技术栈

- Background workers (document)
- AgentDB
- JSDoc/TSDoc
- OpenAPI 3.0
- Claude Code function hooks