---
title: "ruflo-ruos — Run Swarm Agents on ruOS Cloud Desktops"
tags: ["ruos", "cloud", "desktop", "swarm", "agent", "mcp", "ssh"]
category: "plugins/ruflo-ruos"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# ruflo-ruos

Run ruflo swarm agents on **your own ruOS cloud desktops**. The plugin can:

- place an agent on a desktop;
- stream the agent's output back;
- show the agent in ruflo's swarm state;
- hand finished work back for your review.

The plugin is optional. ruflo works the same without it.

Design and threat model: [ADR-405](../../v3/docs/adr/ADR-405-ruos-desktops-as-swarm-hosts.md).

---

## 💡 概述

A bridge between ruflo swarm coordination and ruOS cloud desktops, enabling remote agent execution with MCP/REST transport and per-tenant SSH for desktop-to-desktop communication.

---

## ⚡ 功能

### Transports

| Transport | When | Configure |
|---|---|---|
| ruOS fleet MCP / REST (default) | **The only path from outside your ruOS tenant** (laptops, workstations, CI) | In Claude Code: the connected `mcp__ruos__*` tools. In a terminal: `RUOS_MCP_URL` + `RUOS_MCP_TOKEN`, or the `@cognitum/ruos` login |
| Per-tenant SSH `:2222` | Only desktop to desktop inside your tenant's Fly 6PN | `--transport ssh`, `RUOS_SSH_KEY`, optional `RUOS_SSH_USER`, `RUOS_FLY_APP` |

### Commands

| Command | What it does |
|---|---|
| `/ruflo-ruos:hosts` | Lists your desktops: state, heartbeat, and the ruflo agents on each |
| `/ruflo-ruos:run` | Runs `claude -p` on a desktop, streams the output, and records the agent and its claim |
| `/ruflo-ruos:view` | Delegates to `/ruos view` if installed, otherwise to a view-only `desktop_share` link |
| `/ruflo-ruos:deploy` | Read-only hand-off: branch, HEAD, dirty and ahead counts of a repo, plus a summary for a human to review, merge and deploy |

### CLI

```bash
CLI="node plugins/ruflo-ruos/scripts/cli.mjs"
$CLI status                                   # config + next auto-stop, no network
$CLI hosts
$CLI run --desktop "Work Desktop" --prompt-file task.txt --model sonnet --timeout 900
$CLI run --desktop <id> --prompt-file task.txt --start    # wakes a stopped desktop (billable)
$CLI stop --desktop <id> --run <runId> --confirm
$CLI desktop-stop --desktop <id> --confirm
$CLI attach --desktop <id> --run <runId>     # re-read a run after a restart (HOME persists)
$CLI logs --run <runId>
$CLI build --prompt-file task.txt             # exact desktop_exec strings for the session path
$CLI record start|output|end --run <runId> ...
$CLI deploy-info --desktop <id> --repo projects/app
```

### Status line (mod)

When the ruflo mod (`ruflo-mods`) is loaded, this plugin's mod adds a `ruos` segment to ruflo's status line, e.g. `ruOS 2 agents · Work Desktop`.

---

## 📊 指标

「待补充」

---

## 🏛️ 设计

### Swarm state

Remote agents are recorded with ruflo's own tools: `agent_spawn` with `config.host = {kind: "ruos", desktopId, desktopName, transport, runId}`, then `agent_update`, then `claims_claim`/`claims_release`. The plugin itself writes only to `.claude-flow/ruos/`:

- `events.jsonl`: lifecycle events, with ids and sizes only;
- `audit.jsonl`: one record per run (desktop, command sha256, start, end, exit, bytes);
- `hosts.json`: the current host snapshot;
- `runs/<runId>.log`: the run output, mode 0600.

### Security

- The plugin never contacts the desktop executor on `:17870`, which has no per-tenant auth
- It never calls `desktop_delete` or `secret_delete`
- It never logs your token
- With no credentials, every networked command exits 2 and makes no request

### Cost and auto-stop

ruOS stops cloud desktops at **23:00 America/Toronto on weekdays**. `desktop_keepawake` does not override this. A run that could cross the stop is refused unless you pass `--ignore-autostop`.

---

## 📁 目录

「待补充」

---

## 🔗 资源

- [ADR-405](../../v3/docs/adr/ADR-405-ruos-desktops-as-swarm-hosts.md) — Design and threat model

---

## 🚀 快速开始

「待补充」

---

## ⚙️ 技术栈

- ruOS Fleet MCP / REST API
- SSH (Fly 6PN)
- Claude Code plugin system
- MCP tools: `mcp__ruos__*`