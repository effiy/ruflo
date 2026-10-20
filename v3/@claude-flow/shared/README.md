---
title: "@claude-flow/shared"
tags: ["shared", "utilities", "types", "logging", "config", "errors", "subprocess"]
category: "@claude-flow/shared"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/shared

[![npm version](https://img.shields.io/npm/v/@claude-flow/shared.svg)](https://www.npmjs.com/package/@claude-flow/shared)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/shared.svg)](https://www.npmjs.com/package/@claude-flow/shared)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 💡 概述

Shared utilities and types for Claude Flow V3 -- the foundational library consumed by virtually every other Claude Flow package.

---

## ⚡ 功能

- **Type System** - Common TypeScript interfaces, enums, and type guards
- **Logging** - Structured logging with levels, timestamps, and context
- **Configuration** - Environment-based configuration loading and validation
- **Error Handling** - Custom error classes with codes and metadata
- **File Operations** - Safe file I/O with streaming and path utilities
- **Subprocess** - Managed child process execution with timeouts and event binding
- **Data Utilities** - Deep merge, sorting, filtering, timestamps, IDs

---

## 📊 指标

- **Zero Dependencies** (except TypeScript)
- **~50+ shared types** across the ecosystem
- **~30+ utility functions**
- **100% TypeScript** with strict mode

---

## 🏛️ 设计

### Core Exports

#### Types

| Export | Description |
|--------|-------------|
| `AgentConfig` | Agent configuration interface |
| `AgentStatus` | Agent lifecycle status enum |
| `ToolDefinition` | MCP tool definition type |
| `HookContext` | Hook execution context |
| `PluginManifest` | Plugin metadata |
| `LLMProviderConfig` | Provider configuration |

#### Utilities

| Export | Description |
|--------|-------------|
| `createLogger` | Structured logger factory |
| `loadConfig` | Environment config loader |
| `AppError` / `ValidationError` / `ConfigError` | Error classes |
| `safeReadFile` / `safeWriteFile` | Safe file I/O |
| `execCommand` | Managed subprocess execution |
| `deepMerge` | Deep object merge |
| `generateId` | ID generation |
| `sleep` / `retry` / `timeout` | Async utilities |
| `sortBy` / `groupBy` / `pick` / `omit` | Data utilities |
| `isRecord` / `isString` / `isDefined` | Type guards |

### Error Classes

```typescript
class AppError extends Error {
  code: string;
  context?: Record<string, unknown>;
}

class ValidationError extends AppError {
  issues: { path: string; message: string }[];
}

class ConfigError extends AppError {
  key: string;
}

class NotFoundError extends AppError {}

class TimeoutError extends AppError {}

class AuthError extends AppError {}
```

### Logging

```typescript
import { createLogger } from '@claude-flow/shared';

const log = createLogger('my-module');

log.info('Starting process', { pid: process.pid });
log.warn('Rate limit approaching', { current: 85, limit: 100 });
log.error('Operation failed', new Error('timeout'));
log.debug('Detailed state', { state });
```

### Configuration

```typescript
import { loadConfig } from '@claude-flow/shared';

const config = loadConfig<MyConfig>({
  prefix: 'CF_',
  defaults: { port: 3000, logLevel: 'info' },
  required: ['apiKey'],
  schema: { /* Zod schema */ }
});
```

### File Operations

```typescript
import { safeReadFile, safeWriteFile, createReadStream, createWriteStream } from '@claude-flow/shared';

const content = await safeReadFile('/path/to/file.txt', { encoding: 'utf-8' });
await safeWriteFile('/path/to/output.txt', content);
```

### Subprocess

```typescript
import { execCommand } from '@claude-flow/shared';

const result = await execCommand('git', ['status', '--porcelain'], {
  timeout: 5000,
  cwd: '/project',
  env: { ...process.env, GIT_DIR: '/custom' }
});
// { stdout: 'M file.ts', stderr: '', code: 0 }
```

### Data Utilities

```typescript
import { deepMerge, sortBy, groupBy, pick, omit } from '@claude-flow/shared';

const merged = deepMerge(defaults, overrides);
const sorted = sortBy(items, 'priority', 'desc');
const grouped = groupBy(tasks, 'type');
const subset = pick(obj, ['id', 'name']);
const cleaned = omit(obj, ['password', 'secret']);
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Installation

```bash
npm install @claude-flow/shared
```

### Related Packages

- [@claude-flow/security](../security) - Security utilities built on shared types
- [@claude-flow/plugins](../plugins) - Plugin SDK using shared utilities
- [@claude-flow/providers](../providers) - Provider definitions

### License

MIT

---

## 🚀 快速开始

```typescript
import { createLogger, loadConfig, safeReadFile, execCommand } from '@claude-flow/shared';

const log = createLogger('app');
const config = loadConfig({ prefix: 'CF_' });

log.info('App starting', { config });

const content = await safeReadFile(config.inputFile);
const result = await execCommand('wc', ['-l', config.inputFile]);

log.info('File processed', { lines: result.stdout.trim() });
```

---

## ⚙️ 技术栈

- TypeScript
- Node.js