---
title: "Pair Programming Command"
tags: ["pair-programming", "commands", "claude-flow", "collaboration", "verification", "code-review"]
category: ".claude/commands/pair"
created: "2024-01-01"
updated: "2026-10-20"
source: "internal"
type: "index"
status: "stable"
lifecycle: "active"
---

## 💡 概述

Collaborative development with real-time verification and AI assistance.

The `pair` command enables collaborative pair programming between you and AI agents, with real-time verification, code review, and quality enforcement.

## Usage

```bash
claude-flow pair [--start] [options]
```

## Options

- `--start` - Start new pair programming session
- `--agent <name>` - Specify AI pair partner (default: auto-select)
- `--verify` - Enable real-time verification
- `--threshold <0-1>` - Verification threshold (default: 0.95)
- `--mode <type>` - Programming mode: driver, navigator, switch
- `--focus <area>` - Focus area: refactor, test, debug, implement
- `--language <lang>` - Primary language for session
- `--review` - Enable continuous code review
- `--test` - Run tests after each change

---

## ⚡ 功能

### Modes

**Driver Mode** — You write code, AI provides suggestions and reviews.
```bash
claude-flow pair --start --mode driver
```

**Navigator Mode** — AI writes code, you provide guidance and review.
```bash
claude-flow pair --start --mode navigator
```

**Switch Mode (Default)** — Alternate between driver and navigator roles.
```bash
claude-flow pair --start --mode switch --interval 10m
```

### Real-Time Verification
- Continuous truth checking (0.95 threshold)
- Automatic rollback on verification failure
- Quality gates before commits

### Code Review
- Instant feedback on code changes
- Best practice suggestions
- Security vulnerability detection
- Performance optimization tips

### Test Integration
- Automatic test generation
- Test-driven development support
- Coverage monitoring
- Integration test suggestions

### Collaboration Tools
- Shared context between you and AI
- Session history and replay
- Code explanation on demand
- Learning from your preferences

### Session Management

**Start Session:**
```bash
claude-flow pair --start
claude-flow pair --start \
  --agent expert-coder \
  --verify \
  --test \
  --focus refactor
```

**During Session:**
```commands
/help          - Show available commands
/explain       - Explain current code
/suggest       - Get improvement suggestions
/test          - Run tests
/verify        - Check verification score
/switch        - Switch driver/navigator roles
/focus <area>  - Change focus area
/commit        - Commit with verification
/pause         - Pause session
/resume        - Resume session
/end           - End session
```

**End Session:**
```bash
claude-flow pair --end --save
claude-flow pair --end --report
```

### Examples

**Refactoring Session:**
```bash
claude-flow pair --start --focus refactor --verify --threshold 0.98
```

**Test-Driven Development:**
```bash
claude-flow pair --start --focus test --mode tdd --language javascript
```

**Bug Fixing:**
```bash
claude-flow pair --start --focus debug --agent debugger-expert --test
```

**Code Review Session:**
```bash
claude-flow pair --start --review --verify --agent senior-reviewer
```

### Integration

**With Git:**
```bash
claude-flow pair --start --git --auto-commit
claude-flow pair --start --git --review-commit
```

**With Testing Frameworks:**
```bash
claude-flow pair --start --test-framework jest
claude-flow pair --start --test-framework pytest
```

**With CI/CD:**
```bash
claude-flow pair --start --ci --non-interactive
```

---

## 📊 指标

### Quality Metrics

- **Session Duration**: Total pair programming time
- **Code Quality**: Average truth score during session
- **Productivity**: Lines changed, features completed
- **Learning**: Patterns learned from collaboration
- **Test Coverage**: Coverage improvement during session

### Session Output Example

```
👥 Pair Programming Session Started
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Partner: expert-coder
Mode: Switch (10m intervals)
Focus: Implementation
Verification: ✅ Enabled (0.95)
Testing: ✅ Auto-run
Current Role: DRIVER (you)
Navigator: expert-coder is reviewing...
📝 Working on: src/auth/login.js
Truth Score: 0.972 ✅
Test Coverage: 84% 📈
💡 Suggestion: Consider adding input validation for email field
🔍 Review: Line 23 - Potential SQL injection vulnerability
Type /help for commands or start coding...
```

---

## 🏛️ 设计

### Configuration

Configure pair programming in `.claude-flow/config.json`:

```json
{
  "pair": {
    "defaultAgent": "expert-coder",
    "defaultMode": "switch",
    "switchInterval": "10m",
    "verification": {
      "enabled": true,
      "threshold": 0.95,
      "autoRollback": true
    },
    "testing": {
      "autoRun": true,
      "framework": "jest",
      "coverage": {
        "minimum": 80,
        "enforce": true
      }
    },
    "review": {
      "continuous": true,
      "preCommit": true
    }
  }
}
```

### Best Practices

1. **Start with Clear Goals**: Define what you want to accomplish
2. **Use Verification**: Enable verification for critical code
3. **Test Frequently**: Run tests after significant changes
4. **Review Together**: Use review features for learning
5. **Document Decisions**: AI will help document why choices were made

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Related Commands

- `verify` - Standalone verification
- `truth` - View quality metrics
- `test` - Run test suites
- `review` - Code review tools

---

## 🚀 快速开始

```bash
# Start pair programming session
claude-flow pair --start

# Start with specific agent
claude-flow pair --start --agent senior-dev

# Start with verification
claude-flow pair --start --verify --threshold 0.98
```

---

## ⚙️ 技术栈

- Claude Flow CLI
- Real-time verification engine
- Git integration
- Test framework adapters (Jest, Pytest)