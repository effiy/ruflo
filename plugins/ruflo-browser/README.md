---
title: "ruflo-browser — Session-as-Skill Browser Automation"
tags: ["browser", "playwright", "session", "rvf", "aidefence", "trajectory", "agentdb", "mcp"]
category: "plugins/ruflo-browser"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'proposed'
lifecycle: 'active'
---

# ruflo-browser

Session-as-skill browser automation. Playwright-backed via 23 `mcp__plugin_ruflo-core_ruflo__browser_*` tools, with each session captured as a first-class **RVF cognitive container** holding manifest + trajectory + screenshots + sanitized cookies + findings, indexed in AgentDB and gated by AIDefence.

> **v0.2.0 architecture** — every browser session is now an addressable, replayable, federatable artifact. Status is **Proposed** per [ADR-0001](./docs/adrs/0001-browser-skills-architecture.md).
>
> **Substrate alignment (ADR-122).** This plugin is the user-facing skill layer; the substrate primitives — signed trajectories (Ed25519 + RVF), causal-graph self-healing, AIDefence-attested cookie vault, federated MCTS, Session Capsules, Workflow Compiler — ship in the [`@claude-flow/browser@3.0.0-alpha.4`](https://www.npmjs.com/package/@claude-flow/browser) npm package.

---

## 💡 概述

A browser session is allocated an RVF container at session-start and committed at session-end:

```
<rvf-id>/
├── manifest.yaml         # URL, viewport, profile, runner, lineage
├── trajectory.ndjson     # one line per action via ruvector hooks trajectory-step
├── screenshots/<step>.png
├── snapshots/<step>.json # accessibility trees indexed by navigation
├── dom/                  # optional, when --with-dom
├── cookies.json          # AIDefence-sanitized
└── findings.md           # test verdicts, scrape outputs, injection quarantine
```

Re-open with `rvf ingest <id>`, fork with `rvf derive`, federate with `rvf export`.

---

## ⚡ 功能

### Commands

```bash
/ruflo-browser ls [--query <text>]      # list sessions, AgentDB-indexed
/ruflo-browser show <session-id>        # manifest + trajectory + verdict
/ruflo-browser replay <session-id>      # re-drive trajectory
/ruflo-browser export <session-id>      # rvf export → tar.zst
/ruflo-browser fork <session-id>        # rvf derive → new lineage-tracked session
/ruflo-browser purge <session-id>       # destroy, keep redacted manifest
/ruflo-browser doctor                   # check Playwright, MCP, AgentDB, AIDefence
```

### Skills

| Skill | Purpose |
|-------|---------|
| `browser-record` | Open a named, traced session into an RVF container |
| `browser-replay` | Replay a stored trajectory |
| `browser-extract` | Run a stored recipe or one-shot extraction. PII-scanned |
| `browser-login` | Drive an auth flow once, sanitize+vault cookies for reuse |
| `browser-form-fill` | Form interaction with field-name → value mapping |
| `browser-screenshot-diff` | Pixel + DOM diff between two session screenshots |
| `browser-auth-flow` | Probe an auth flow for redirect leaks, missing CSRF |
| `browser-test` | UI test recipe — composes `browser-record` + `browser-replay` |

### MCP surface (23 tools)

18 browser interaction primitives + 5 new `browser_session_*` lifecycle tools:

| Tool | Purpose |
|------|---------|
| `browser_session_record` | RVF allocate + `trajectory-begin` + `agent-browser open` |
| `browser_session_end` | `trajectory-end` with verdict + `rvf compact` + AgentDB index |
| `browser_session_replay` | RVF derive child container + load trajectory steps |
| `browser_template_apply` | Fetch a recipe from `browser-templates` AgentDB namespace |
| `browser_cookie_use` | Fetch an opaque vault handle from `browser-cookies` |

---

## 📊 指标

### Memory layer (AgentDB)

| Namespace | Key | Value | Purpose |
|-----------|-----|-------|---------|
| `browser-sessions` | `<rvf-id>` | manifest summary + verdict + tags | session index |
| `browser-selectors` | `<host>:<intent>` | selector, ref, snapshot-hash, last-success | DOM drift survival |
| `browser-templates` | `<template-name>` | scrape recipe with selector chain + post-process | recipe storage |
| `browser-cookies` | `<host>` | claims-gated cookie blob + expiry + AIDefence verdict | cookie reuse |

---

## 🏛️ 设计

### AIDefence gates (mandatory)

1. **Pre-storage scan** — every scraped string passes `aidefence_has_pii` before AgentDB store.
2. **Cookie sanitization** — `aidefence_scan` flags high-entropy strings; vault them in `browser-cookies`.
3. **Prompt-injection check** — extracted text returning to an LLM passes `aidefence_is_safe`. Hits get quarantined to `findings.md`.

### As a mod

Function hooks (ADR-445 pattern):
- **Tool guard** (default on) — refuses browser_open to file/javascript/data/chrome URLs, URLs with credentials, and unsafe Chrome flags; refuses browser_eval scripts reading cookies/storage.
- **`/browser-mod`** — answers locally: `status`, `scan <js>`, `url <url>`.
- **Status file** `.claude-flow/browser-mod/status.json`

---

## 📁 目录

「待补充」

---

## 🔗 资源

- [`ADR-0001` — Adopt session-as-skill architecture](./docs/adrs/0001-browser-skills-architecture.md)
- Related: `ruflo-ruvector`, `ruflo-agentdb`, `ruflo-aidefence`, `ruflo-federation`

---

## 🚀 快速开始

### Install

```
/plugin marketplace add ruvnet/ruflo
/plugin install ruflo-browser@ruflo
```

### Verification

```bash
# Structural smoke (fast, offline)
bash plugins/ruflo-browser/scripts/smoke.sh
# Expected: "13 passed, 0 failed"

# Replay spike (interactive, online — pre-Accept gate)
bash plugins/ruflo-browser/scripts/replay-spike.sh
```

---

## ⚙️ 技术栈

- Playwright (browser automation)
- RVF cognitive containers
- AgentDB (session indexing)
- AIDefence (PII / prompt-injection gates)
- ruvector (trajectory hooks)
- MCP protocol