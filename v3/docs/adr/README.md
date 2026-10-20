---
title: "Architecture Decision Records (ADR)"
tags: ["adr", "architecture", "decisions", "v3", "ddd", "governance"]
category: "docs/adr"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# Architecture Decision Records (ADR)

---

## 💡 概述

Architecture Decision Records for Claude Flow V3 - indexed catalog of all architectural decisions, their status, and cross-references.

---

## ⚡ 功能

This directory contains all Architecture Decision Records (ADRs) documenting significant architectural decisions made during the development of Claude Flow V3.

### ADR Index by Category

#### Architecture & Patterns

| ADR | Title | Status |
|-----|-------|--------|
| ADR-001 | Eliminate code duplication via agentic-flow | Accepted |
| ADR-026 | 3-Tier model routing (codemod / haiku / sonnet-opus) | Accepted |
| ADR-143 | Deterministic codemod classification | Accepted |
| ADR-148 | MetaHarness router integration | Accepted |
| ADR-149 | Cost-optimal neural routing gate | Accepted |
| ADR-150 | MetaHarness integration surfaces | Accepted |
| ADR-152 | Genome similarity and structural-distance gate | Accepted |
| ADR-235 | GEPA learning run integration (metaharness@0.3.0) | Accepted |
| ADR-236 | Host-normalization for meta-llm gateway | Proposed |
| ADR-322 | Darwin flywheel concurrent evaluation | Accepted |

#### V3 Core

| ADR | Title | Status |
|-----|-------|--------|
| ADR-153 | Benchmark suite gate for evolve | Accepted |
| ADR-026 | 3-Tier model routing | Accepted |

---

## 📊 指标

| Metric | Value |
|--------|-------|
| Total ADRs | 「有待统计」 |
| Accepted | Majority |
| Proposed | 「有待统计」 |
| Deprecated | 「有待统计」 |
| Superseded | 「有待统计」 |

---

## 🏛️ 设计

### ADR Format

Each ADR follows the standard format:

```
# ADR-NNN: Title
## Status
[Proposed | Accepted | Deprecated | Superseded]

## Context
[What is the issue motivating this decision?]

## Decision
[What is the change we're proposing and/or doing?]

## Consequences
[What becomes easier or more difficult?]
```

### Status Lifecycle

```
Proposed → Accepted → Deprecated | Superseded
```

### How to Create a New ADR

1. Copy the template from `TEMPLATE.md`
2. Name it `ADR-NNN-title-in-kebab-case.md`
3. Start status as `Proposed`
4. Submit for review
5. Update status after decision

---

## 📁 目录

「待补充」

---

## 🔗 资源

- [ADR GitHub](https://adr.github.io/) - ADR methodology
- [Architecture](https://github.com/ruvnet/claude-flow) - Main project architecture
- [CLAUDE.md](../../CLAUDE.md) - Root development configuration

---

## 🚀 快速开始

To browse ADRs, explore the files in this directory. Each ADR is a standalone markdown file documenting a specific architectural decision with context, decision, and consequences.

---

## ⚙️ 技术栈

- Markdown
- ADR methodology (adr.github.io)