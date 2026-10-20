---
title: "V3 Plugin System"
tags: ["plugin", "microkernel", "ADR-004", "DDD", "extension", "lifecycle", "dependency"]
category: "implementation/plugins"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# Claude-Flow V3 Plugin System

> Domain-Driven Design Plugin-Based Architecture (ADR-004)

---

## 💡 概述

The V3 Plugin System implements a **microkernel architecture** enabling modular extension points for:
- Custom agent types
- Task types
- MCP tools
- CLI commands
- Memory backends

---

## ⚡ 功能

### Core Components

#### 1. ClaudeFlowPlugin Interface

All plugins must implement this interface:

```typescript
interface ClaudeFlowPlugin {
  // Required
  readonly name: string;
  readonly version: string;
  initialize(context: PluginContext): Promise<void>;
  shutdown(): Promise<void>;

  // Optional extensions
  dependencies?: string[];
  description?: string;
  author?: string;

  // Extension points
  registerAgentTypes?(): AgentTypeDefinition[];
  registerTaskTypes?(): TaskTypeDefinition[];
  registerMCPTools?(): MCPToolDefinition[];
  registerCLICommands?(): CLICommandDefinition[];
  registerMemoryBackends?(): MemoryBackendFactory[];

  // Health monitoring
  healthCheck?(): Promise<boolean>;
}
```

#### 2. PluginContext

Provided during initialization with access to core services:

```typescript
interface PluginContext {
  config: PluginConfig;      // Plugin configuration
  eventBus: IEventBus;       // Pub/sub events
  logger: ILogger;           // Logging
  services: ServiceContainer; // DI container
}
```

#### 3. PluginRegistry

Central registry for plugin and extension tracking:

```typescript
const registry = new PluginRegistry();

// Plugin management
registry.registerPlugin(plugin, 'uninitialized', context);
registry.getPlugin('my-plugin');
registry.getAllPlugins();
registry.getPluginsByState('initialized');

// Extension queries
registry.getAllAgentTypes();
registry.getAllMCPTools();
registry.getAllCLICommands();
registry.getAllMemoryBackends();

// Status
registry.getStatusSummary();
```

#### 4. PluginLoader

Handles lifecycle and dependency resolution:

```typescript
const loader = new PluginLoader(registry, {
  initializationTimeout: 30000,
  shutdownTimeout: 10000,
  parallelInitialization: false,
  strictDependencies: true,
  enableHealthChecks: true,
  healthCheckInterval: 60000,
});

// Load plugins
const result = await loader.loadPlugins([pluginA, pluginB], context);
console.log(`Loaded: ${result.successful.join(', ')}`);

// Unload
await loader.unloadPlugin('my-plugin');
await loader.unloadAll();
```

### Extension Points

#### Agent Types

```typescript
registerAgentTypes(): AgentTypeDefinition[] {
  return [{
    type: 'custom-agent',
    name: 'Custom Agent',
    description: 'Specialized agent for custom tasks',
    defaultConfig: {
      model: 'claude-3-opus',
      maxTokens: 4096,
    },
    requiredCapabilities: ['custom-capability'],
  }];
}
```

#### Task Types

```typescript
registerTaskTypes(): TaskTypeDefinition[] {
  return [{
    type: 'custom-task',
    name: 'Custom Task',
    description: 'Specialized task processing',
    defaultPriority: 50,
    defaultTimeout: 60000,
    inputSchema: {
      type: 'object',
      properties: {
        input: { type: 'string' }
      },
      required: ['input']
    }
  }];
}
```

#### MCP Tools

```typescript
registerMCPTools(): MCPToolDefinition[] {
  return [{
    name: 'custom_tool',
    description: 'Custom MCP tool',
    inputSchema: {
      type: 'object',
      properties: {
        param: { type: 'string', description: 'Input parameter' }
      },
      required: ['param']
    },
    handler: async (input, context) => {
      return { result: `Processed: ${input.param}` };
    }
  }];
}
```

#### CLI Commands

```typescript
registerCLICommands(): CLICommandDefinition[] {
  return [{
    name: 'custom-cmd',
    description: 'Custom CLI command',
    aliases: ['cc'],
    options: [
      {
        name: 'verbose',
        short: 'v',
        description: 'Enable verbose output',
        type: 'boolean',
        default: false
      }
    ],
    handler: async (args) => {
      console.log('Custom command executed', args);
    }
  }];
}
```

#### Memory Backends

```typescript
registerMemoryBackends(): MemoryBackendFactory[] {
  return [{
    name: 'custom-backend',
    description: 'Custom memory backend',
    capabilities: {
      supportsVectorSearch: true,
      supportsFullText: true,
      supportsTransactions: false,
      supportsPersistence: true
    },
    create: async (config) => new CustomMemoryBackend(config)
  }];
}
```

### Dependency Management

Plugins can declare dependencies on other plugins:

```typescript
class DependentPlugin implements ClaudeFlowPlugin {
  readonly name = 'dependent-plugin';
  readonly version = '1.0.0';
  readonly dependencies = ['base-plugin', 'auth-plugin'];

  async initialize(context: PluginContext): Promise<void> {
    // Dependencies guaranteed to be initialized first
  }
}
```

The PluginLoader:
1. Builds dependency graph
2. Detects circular dependencies (throws error)
3. Topologically sorts for correct initialization order
4. Initializes in dependency order (or parallel by level)

### Official Plugins

Located in `@claude-flow/shared/src/plugins/official/`:

#### Maestro Plugin

Workflow orchestration with phase management:
- Multi-phase workflow execution
- Progress tracking
- Error handling and recovery

#### Hive Mind Plugin

Collective intelligence coordination:
- Shared memory across agents
- Pattern learning
- Consensus mechanisms

### Error Handling

```typescript
import { PluginError, PluginErrorCode } from '@claude-flow/shared';

// Error codes
type PluginErrorCode =
  | 'INITIALIZATION_FAILED'
  | 'SHUTDOWN_FAILED'
  | 'DEPENDENCY_NOT_FOUND'
  | 'CIRCULAR_DEPENDENCY'
  | 'INVALID_PLUGIN'
  | 'DUPLICATE_PLUGIN'
  | 'HEALTH_CHECK_FAILED';

// Throwing errors
throw new PluginError(
  'Failed to connect to database',
  'my-plugin',
  'INITIALIZATION_FAILED',
  originalError
);
```

### Configuration

Plugins receive configuration through PluginContext:

```typescript
interface PluginConfig {
  [key: string]: unknown;

  features?: Record<string, boolean>;

  resources?: {
    maxMemoryMb?: number;
    maxCpuPercent?: number;
  };
}
```

### Best Practices

1. **Keep plugins focused** - Single responsibility per plugin
2. **Declare dependencies explicitly** - Don't rely on load order
3. **Implement health checks** - For production monitoring
4. **Handle shutdown gracefully** - Clean up resources
5. **Use logging** - Via context.logger, not console
6. **Validate configuration** - Early in initialize()
7. **Document extension points** - Clear descriptions and schemas

---

## 📊 指标

### Plugin Lifecycle States

| State | Description |
|-------|-------------|
| `uninitialized` | Registered but not yet initialized |
| `initializing` | Currently running initialize() |
| `initialized` | Successfully initialized and active |
| `shutting-down` | Currently running shutdown() |
| `shutdown` | Successfully shut down |
| `error` | Error during lifecycle transition |

---

## 🏛️ 设计

### Architecture

```
┌─────────────────────────────────────────────────────┐
│                  Plugin Loader                       │
│  - Dependency resolution                            │
│  - Lifecycle management                             │
│  - Health checks                                    │
└──────────────────────┬──────────────────────────────┘
                       │
┌──────────────────────┴──────────────────────────────┐
│                 Plugin Registry                      │
│  - Plugin tracking                                  │
│  - Extension point registration                     │
│  - Status/metrics collection                        │
└──────────────────────┬──────────────────────────────┘
                       │
    ┌──────────────────┼──────────────────┐
    │                  │                  │
┌───┴───┐         ┌────┴────┐        ┌────┴────┐
│Plugin │         │ Plugin  │        │ Plugin  │
│   A   │         │    B    │        │    C    │
└───────┘         └─────────┘        └─────────┘
```

### Lifecycle Flow

```
uninitialized → initializing → initialized → shutting-down → shutdown
                     │                              │
                     └──────── error ◄─────────────┘
```

---

## 📁 目录

### Files Reference

| File | Purpose |
|------|---------|
| `plugin-interface.ts` | Core interfaces and types |
| `plugin-registry.ts` | Extension registration |
| `plugin-loader.ts` | Lifecycle management |
| `plugins/official/` | Built-in plugins |

---

## 🔗 资源

### Related ADRs

- **ADR-004**: Plugin-Based Architecture (Microkernel Pattern)
- **ADR-005**: MCP-First API Design
- **ADR-007**: Event Sourcing for State Changes

---

## 🚀 快速开始

```typescript
import { ClaudeFlowPlugin, PluginContext } from '@claude-flow/shared';

class MyPlugin implements ClaudeFlowPlugin {
  readonly name = 'my-plugin';
  readonly version = '1.0.0';

  async initialize(context: PluginContext): Promise<void> {
    context.logger.info('MyPlugin initialized');
  }

  async shutdown(): Promise<void> {
    // Cleanup
  }
}
```

---

## ⚙️ 技术栈

「待补充」