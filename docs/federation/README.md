---
title: "Ruflo Federation — User Guide"
tags: ["federation", "agent", "peering", "ed25519", "trust", "circuit-breaker", "wireguard", "mesh"]
category: "docs/federation"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'guide'
status: 'stable'
lifecycle: 'active'
---

# Ruflo Federation — User Guide

> Cross-installation agent peering with built-in cost limits, circuit breaker, signed envelopes, and (as of alpha.14) opt-in WireGuard mesh layer governed by federation trust.

This guide walks through what federation is, when to use it, and how to set it up. For the architectural backstory and per-phase release notes see the [companion gist](https://gist.github.com/ruvnet/3b5111a2ea7e450ff262ce96e88560bf).

---

## 💡 概述

Federation lets two or more Ruflo installations — your mac, a server, a teammate's laptop — discover each other, exchange signed manifests, and send messages between them with bounded cost and per-peer trust gates. Key properties:

- **Ed25519 identity** — each node holds a private key; peers exchange Ed25519-signed manifests. No central directory.
- **Five-level trust ladder** — `UNTRUSTED → VERIFIED → ATTESTED → TRUSTED → PRIVILEGED`. Each level unlocks a wider set of operations (`discovery`, `send`, `share-context`, `remote-spawn`, ...).
- **Per-peer budget + circuit breaker** — bounded tokens/USD per peer. Sustained failures auto-SUSPEND; further failures EVICT. ADR-097.
- **PII pipeline + audit trail** — every cross-peer envelope passes through PII detection. Every state transition is auditable.
- **Real wire transport** — WSS with permessage-deflate compression, optional cert pinning, stream multiplexing. ADR-104.
- **Optional WG mesh layer** — opt-in opaque packet-layer reachability that follows federation trust changes. Compromised peer auto-isolated at L3 when the breaker fires. ADR-111.

---

## ⚡ 功能

### When to use federation

| Use case | Fit |
|---|---|
| Two laptops collaborating on a project, want bounded cost sharing + audit | ✅ |
| Personal home server agent ↔ travel laptop | ✅ |
| Team of 5 engineers sharing memory/skills across machines | ✅ |
| Mobile / Windows / sub-50 peers with NAT issues | ✅ over Tailscale + federation |
| Public-internet exposed agent endpoints | ✅ with TLS cert pinning (ADR-107) |
| Internal HR/finance multi-agent workflow with strict access tiers | ✅ with PRIVILEGED gating + audit |
| Replacing Slack/Discord — no | ❌ federation is for agent-to-agent, not human chat |
| Untrusted-internet messaging without identity vetting | ❌ — trust ladder must be bootstrapped out-of-band |

### MCP tools at a glance

| Tool | What it does | Trust gate |
|---|---|---|
| `federation_init` | Initialize this node | — |
| `federation_join` | Join a peer by endpoint | — |
| `federation_peers` | List discovered peers | — |
| `federation_send` | Send a typed message to a peer | per-peer (varies) |
| `federation_query` | Synchronous query → response | `ATTESTED+` |
| `federation_status` | Current node + peer trust summary | — |
| `federation_trust` | View / adjust trust levels | operator |
| `federation_audit` | Read audit log | operator |
| `federation_breaker_status` | Per-peer state, when changed, why | — |
| `federation_evict` | Operator manual evict | operator |
| `federation_reactivate` | Operator manual reactivate | operator |
| `federation_report_spend` | Report cost of a completed call | integrator |
| `federation_consensus` | Federated proposal across peers | varies |
| **`federation_wg_status`** | (ADR-111) Per-peer mesh state | — |
| **`federation_wg_attest`** | (ADR-111) Operator-signed witness entry | operator |
| **`federation_wg_keyrotate`** | (ADR-111) Rotate WG keypair | operator + `confirm:true` |

### Trust levels

| Level | Capabilities (federation) | WG reachability (if ADR-111 active) |
|---|---|---|
| `UNTRUSTED` | `discovery` | Excluded from mesh — drop all |
| `VERIFIED` | `+ status, ping` | Discovery port (9100) only |
| `ATTESTED` | `+ send, receive, query-redacted` | + federation messaging (9101-9199) |
| `TRUSTED` | `+ share-context, collaborative-task` | + ssh (22), services (80/443) |
| `PRIVILEGED` | `+ full-memory, remote-spawn` | Full mesh |

---

## 📊 指标

### Circuit breaker

If a peer's failure ratio or cost spend exceeds the policy:

```
ACTIVE → SUSPENDED → EVICTED
   ↑          │
   └─────────operator-only─────────┘
```

- **SUSPEND**: peer's outbound sends short-circuit (`PEER_SUSPENDED`). Existing sessions continue; new sends rejected. Auto-eviction on continued failures.
- **EVICT**: peer's outbound sends short-circuit (`PEER_EVICTED`). Session terminated. Operator must explicitly reactivate.
- The breaker **does not auto-reactivate**. The integrator's health probe is responsible for confirming the peer is healthy and calling `federation_reactivate`.

---

## 🏛️ 设计

### ADR-111 — WireGuard mesh (opt-in)

- WG keypair generated alongside the federation key
- Mesh IP derived deterministically from `nodeId`
- WG identity published inside the same Ed25519-signed manifest
- Federation breaker SUSPEND → `wg set ... allowed-ips ""`
- Federation breaker EVICT → `wg set ... remove`
- Operator reactivate → AllowedIPs restored

See [`docs/federation/phase7-mesh-bringup.md`](./phase7-mesh-bringup.md) for the cross-OS bringup procedure.

### Anti-goals

- **Replacement for Slack/Discord.** Federation moves agent envelopes, not human chat.
- **Public internet without identity vetting.** Trust ladder bootstrapping is your responsibility.
- **NAT traversal magic.** Use Tailscale for that; federation rides on top.
- **A general-purpose RPC framework.** It's specifically for AI agents with cost-aware budgeting.

---

## 📁 目录

| What | Path |
|---|---|
| Plugin source | `v3/@claude-flow/plugin-agent-federation/src/` |
| Tests | `v3/@claude-flow/plugin-agent-federation/__tests__/` |
| ADRs | `v3/docs/adr/ADR-{097,104,105,106,107,109,110,111}-*.md` |
| Phase 7 staging script | `v3/@claude-flow/plugin-agent-federation/scripts/phase7-stage.mjs` |
| Witness signing | `plugins/ruflo-core/scripts/witness/` |

---

## 🔗 资源

### Releases

| Version | What landed |
|---|---|
| `1.0.0-alpha.9` | First user-visible release |
| `1.0.0-alpha.10` | ADR-097 Phases 2.a-4 + ADR-104 transport + ADR-109 inbound dispatcher |
| `1.0.0-alpha.11-12` | ADR-109 sig verify, ADR-104 compression, ADR-107 TLS cert pinning |
| `1.0.0-alpha.13` | ADR-104 stream multiplexing + ADR-110 MemorySpendReporter |
| **`1.0.0-alpha.14`** | **ADR-111 Phases 1-3 (WG mesh foundation)** |
| `1.0.0-alpha.15` (in flight) | ADR-111 Phases 4-6 (firewall + witness + MCP tools) — PR #1895 |

### Related ADRs

- [ADR-097](../../v3/docs/adr/ADR-097-federation-budget-circuit-breaker.md)
- [ADR-104](../../v3/docs/adr/ADR-104-federation-wire-transport.md)
- [ADR-105](../../v3/docs/adr/ADR-105-federation-v1-state-snapshot.md)
- [ADR-106](../../v3/docs/adr/ADR-106-peer-discovery.md)
- [ADR-107](../../v3/docs/adr/ADR-107-federation-tls.md)
- [ADR-109](../../v3/docs/adr/ADR-109-federation-inbound-dispatcher.md)
- [ADR-110](../../v3/docs/adr/ADR-110-federation-memory-spend-reporter.md)
- [**ADR-111**](../../v3/docs/adr/ADR-111-federation-wg-mesh.md)

### Support
- Issues: https://github.com/ruvnet/ruflo/issues
- Tracking issue (ADR-111): [#1879](https://github.com/ruvnet/ruflo/issues/1879)

---

## 🚀 快速开始

### 1. Install

```bash
npx ruflo@latest
npx ruflo plugins install @claude-flow/plugin-agent-federation
```

### 2. Initialize a node

```bash
npx claude-flow@v3alpha agent spawn -t federation --name fed-1
```

### 3. Join a peer and send

```json
{"tool": "federation_join", "params": {"endpoint": "ws://other-host.tailnet:9100"}}
{"tool": "federation_send", "params": {"targetNodeId": "other-host", "messageType": "agent-handoff", "payload": {"task": "Investigate the failing integration test"}}}
```

### Enabling ADR-111

```bash
node v3/@claude-flow/plugin-agent-federation/scripts/phase7-stage.mjs \
  <localNodeId> <peerNodeId> <peerPubkey> <peerMeshIP> <peerEndpoint>
```

### Using `claude -p` headless mode

```bash
# On originating host
claude -p --model haiku --max-budget-usd 0.20 --output-format text \
  "Federation MCP tools to verify cross-machine peering health: name 3. One line."

# Cross-machine task handoff
claude -p --model sonnet --output-format json --resume <session> \
  "Use federation_send to dispatch this task to ruvultra: analyze the failing test"
```

---

## ⚙️ 技术栈

- Ed25519 keypairs and signatures
- WSS transport with permessage-deflate
- mTLS + cert pinning
- WireGuard mesh (opt-in)
- Nftables / pf firewall projection