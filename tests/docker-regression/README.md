---
title: "Claude-Flow Deep Regression Test Suite"
tags: ["testing", "docker", "regression", "ci", "integration", "benchmark", "security"]
category: "tests"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# Claude-Flow Deep Regression Test Suite

Comprehensive Docker-based test suite for verifying all Claude-Flow capabilities.

---

## 💡 概述

Docker-based regression test suite with 1000+ test cases across 12 categories covering CLI commands, MCP server, agents, swarms, hooks, plugins, security, memory, workers, performance, unit tests, and integration tests.

---

## ⚡ 功能

### Test Categories

| Category | Tests | Description |
|----------|-------|-------------|
| CLI Commands | 30+ | All CLI commands and options |
| MCP Server | 35+ | MCP tools and protocol |
| Agents (54+) | 60+ | All agent types and spawn |
| Swarm | 45+ | Topologies and coordination |
| Hooks | 50+ | Self-learning and routing |
| Plugins | 70+ | RuVector WASM plugins |
| Security | 55+ | Security features and CVE |
| Memory | 50+ | AgentDB and HNSW |
| Workers | 60+ | Background workers |
| Performance | 45+ | Benchmarks and targets |
| Unit Tests | 424+ | V3 package tests |
| Integration | 65+ | E2E workflows |

**Total: 1000+ test cases**

---

## 📊 指标

### Test Coverage

#### V3 Packages

| Package | Tests | Pass Rate |
|---------|-------|-----------|
| @claude-flow/hooks | 112 | 100% |
| @claude-flow/plugins | 142 | 100% |
| @claude-flow/security | 47 | 100% |
| @claude-flow/swarm | 89 | 100% |
| @claude-flow/cli | 34 | 100% |

#### Feature Coverage

- ✅ All 54+ agents
- ✅ All 7 swarm topologies
- ✅ All MCP tools (27+)
- ✅ All hooks commands
- ✅ All security features
- ✅ All memory operations
- ✅ All background workers (10)
- ✅ All performance targets

---

## 🏛️ 设计

### Directory Structure

```
tests/docker-regression/
├── Dockerfile              # Test environment
├── docker-compose.yml      # Orchestration
├── README.md               # This file
├── scripts/
│   ├── run-all-tests.sh    # Main runner
│   ├── test-cli-commands.sh
│   ├── test-mcp-server.sh
│   ├── test-agents.sh
│   ├── test-swarm.sh
│   ├── test-hooks.sh
│   ├── test-plugins.sh
│   ├── test-security.sh
│   ├── test-memory.sh
│   ├── test-workers.sh
│   ├── test-performance.sh
│   ├── run-unit-tests.sh
│   ├── run-integration-tests.sh
│   ├── run-benchmark-tests.sh
│   ├── run-security-tests.sh
│   └── test-utils.sh
├── fixtures/
│   ├── sample-code.ts
│   └── sample-patterns.json
└── reports/                # Generated reports
```

### Reports

Reports are generated in `/app/reports` (or `./reports` locally):
- `regression_report_TIMESTAMP.json` - Full JSON report
- `summary_TIMESTAMP.txt` - Human-readable summary
- `benchmark_TIMESTAMP.json` - Performance metrics
- `security_TIMESTAMP.json` - Security scan results

---

## 📁 目录

「待补充」

---

## 🔗 资源

「待补充」

---

## 🚀 快速开始

```bash
# Run all tests
docker-compose up --build test-runner

# Run specific test categories
docker-compose up --build unit-tests
docker-compose up --build integration-tests
docker-compose up --build benchmark-tests
docker-compose up --build security-tests

# Without Docker
cd tests/docker-regression
bash scripts/run-all-tests.sh
```

### CI/CD Integration

#### GitHub Actions

```yaml
jobs:
  regression-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run Regression Tests
        run: |
          cd tests/docker-regression
          docker-compose up --build --exit-code-from test-runner
      - name: Upload Reports
        uses: actions/upload-artifact@v4
        with:
          name: test-reports
          path: tests/docker-regression/reports/
```

### Troubleshooting

- **Docker Build Fails**: `docker-compose build --no-cache`
- **Tests Timeout**: `COMPOSE_HTTP_TIMEOUT=300 docker-compose up --build`
- **MCP Server Not Starting**: Check port with `lsof -i :3000`
- **Memory Issues**: Reduce parallel tests

---

## ⚙️ 技术栈

- Docker + Docker Compose
- Node.js >= 18
- Environment variables for configuration:
  - `TEST_REPORT_PATH`, `CLAUDE_FLOW_MODE`, `CLAUDE_FLOW_MEMORY_PATH`
  - `CLAUDE_FLOW_LOG_LEVEL`, `CLAUDE_FLOW_MAX_AGENTS`
  - `MCP_SERVER_HOST`, `MCP_SERVER_PORT`