---
title: "ruflo-deepseek-harness"
tags: ["deepseek", "plugin", "mod"]
category: "plugins"
created: "2024-01-01"
updated: "2026-10-20"
source: "internal"
type: "index"
status: "stable"
lifecycle: "active"
---

# ruflo-deepseek-harness

## 💡 概述

DeepSeek chat and reasoning completions as ruflo skills (see `commands/ruflo-deepseek-harness.md`).

---

## ⚡ 功能

### As a mod

Since this version the plugin is also a function-hook mod (ADR-445 pattern; needs Claude Code 2.1.287 or later). It never calls the network or spawns a process, and it only tightens: it can refuse a call, never allow one.

- **Guard** (default on): refuses Bash commands that run this plugin's `chat.mjs`/`reason.mjs` (they post the prompt to api.deepseek.com) that hold a key, token or password. The reason names the kind of secret, never the value.
- **`/deepseek-mod`**: answered locally, no model turn: `status`, `scan <text>` (would the guard refuse this?).
- **Status file**: `.claude-flow/deepseek-mod/status.json` (`{version, updatedMs, guard, blocked}`), written at session start and when a call is blocked.
- **Option**: `guard` (`on` | `off`, default `on`) in the plugin's `userConfig`.

---

## 📊 指标

「待补充」

---

## 🏛️ 设计

「待补充」

---

## 📁 目录

```
plugins/ruflo-deepseek-harness/
├── commands/
│   └── ruflo-deepseek-harness.md
├── scripts/
│   └── smoke.sh
└── README.md
```

---

## 🔗 资源

「待补充」

---

## 🚀 快速开始

Test it: `claude plugin test plugins/ruflo-deepseek-harness` and `bash plugins/ruflo-deepseek-harness/scripts/smoke.sh`.

---

## ⚙️ 技术栈

- Claude Code 2.1.287+
- ADR-445 function-hook mod pattern