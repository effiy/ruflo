---
title: ".agents — Agent Configuration & Skills"
tags: ["agents", "codex", "skills", "configuration"]
category: "infrastructure/config"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# .agents Directory

This directory contains agent configuration and skills for OpenAI Codex CLI.

---

## 💡 概述

「待补充」

---

## ⚡ 功能

- **Configuration**: `config.toml` controls model selection, approval policies, sandbox modes, MCP server connections, and skills configuration
- **Skills**: Invoked via `$skill-name` syntax with YAML frontmatter metadata, trigger/skip conditions, commands and examples

---

## 📊 指标

「待补充」

---

## 🏛️ 设计

```
.agents/
  config.toml     # Main configuration file
  skills/         # Skill definitions
    skill-name/
      SKILL.md    # Skill instructions
      scripts/    # Optional scripts
      docs/       # Optional documentation
  README.md       # This file
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

- Main instructions: `AGENTS.md` (project root)
- Local overrides: `.codex/AGENTS.override.md` (gitignored)
- Claude Flow: https://github.com/ruvnet/claude-flow

---

## 🚀 快速开始

「待补充」

---

## ⚙️ 技术栈

「待补充」