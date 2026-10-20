---
title: "V3 Hooks System Implementation"
tags: ["hooks", "event-driven", "ReasoningBank", "swarm", "MCP", "CLI", "daemon", "statusline"]
category: "implementation/hooks"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# V3 Hooks System Implementation

---

## 💡 概述

The V3 Hooks System provides a comprehensive event-driven architecture for intercepting, modifying, and recording operations throughout the claude-flow lifecycle. It integrates with the **ReasoningBank** neural learning system to enable self-improving agent behaviors.

---

## ⚡ 功能

### Supported Events

| Event | Description | Trigger Point |
|-------|-------------|---------------|
| `PreToolUse` | Before any tool is called | Before Read, Write, Edit, Bash, etc. |
| `PostToolUse` | After tool completes | After tool returns result |
| `PreEdit` | Before file edit | Before Edit/Write operations |
| `PostEdit` | After file edit | After Edit/Write completes |
| `PreCommand` | Before bash command | Before Bash tool execution |
| `PostCommand` | After bash command | After Bash returns |
| `PreTask` | Before task starts | Task assignment |
| `PostTask` | After task completes | Task completion/failure |
| `SessionStart` | Session begins | MCP session initialization |
| `SessionEnd` | Session ends | MCP session shutdown |

### Priority Levels

```typescript
enum HookPriority {
  Critical = 1000,  // Security, validation
  High = 100,       // Pre-processing
  Normal = 50,      // Standard hooks
  Low = 10,         // Logging, metrics
  Background = 1,   // Async operations
}
```

### Hook Registry

```typescript
import { createHookRegistry, HookEvent, HookPriority } from '@claude-flow/shared';

const registry = createHookRegistry();

// Register a hook
const hookId = registry.register(
  HookEvent.PreToolUse,
  async (context) => {
    console.log(`Tool ${context.tool?.name} about to be called`);
    return { success: true };
  },
  HookPriority.High
);

// Disable/enable hooks
registry.disable(hookId);
registry.enable(hookId);

// Get statistics
const stats = registry.getStats();
```

### Hook Executor

```typescript
import { createHookExecutor, HookContext } from '@claude-flow/shared';

const executor = createHookExecutor(registry, eventBus);

const context: HookContext = {
  event: HookEvent.PreToolUse,
  timestamp: new Date(),
  tool: { name: 'Read', parameters: { path: 'file.ts' } },
};

// Execute hooks
const result = await executor.execute(HookEvent.PreToolUse, context, {
  continueOnError: true,
});

// Execute with timeout
const timedResult = await executor.executeWithTimeout(
  HookEvent.PreToolUse,
  context,
  5000
);
```

### MCP Tools

| Tool Name | Description |
|-----------|-------------|
| `hooks/pre-edit` | Get context and suggestions before file edits |
| `hooks/post-edit` | Record edit outcomes for learning |
| `hooks/pre-command` | Risk assessment before command execution |
| `hooks/post-command` | Record command outcomes |
| `hooks/route` | Route task to optimal agent |
| `hooks/explain` | Explain routing decision with transparency |
| `hooks/pretrain` | Bootstrap intelligence from repository |
| `hooks/metrics` | Get learning metrics and statistics |
| `hooks/list` | List registered hooks |

### CLI Commands

```bash
# Pre/Post Edit Hooks
npx claude-flow hooks pre-edit <filePath> [--operation modify]
npx claude-flow hooks post-edit <filePath> --success true

# Pre/Post Command Hooks
npx claude-flow hooks pre-command "npm test"
npx claude-flow hooks post-command "npm test" --success true --exit-code 0

# Task Routing
npx claude-flow hooks route "Implement user authentication"
npx claude-flow hooks explain "Implement user authentication" --verbose

# Intelligence Bootstrap
npx claude-flow hooks pretrain [--include-git --include-deps]
npx claude-flow hooks build-agents [--focus security]

# Metrics & Management
npx claude-flow hooks metrics [--category routing]
npx claude-flow hooks list [--category pre-edit]
npx claude-flow hooks transfer <sourceProject>
```

### Swarm Communication Hooks

V3 introduces **SwarmCommunication** for agent-to-agent coordination within swarms.

#### Agent-to-Agent Messaging

```typescript
import { swarmComm } from '@claude-flow/hooks';

await swarmComm.initialize();

// Send to specific agent
await swarmComm.sendMessage('security-auditor', 'Found auth vulnerability', {
  type: 'context',
  priority: 'high',
});

// Broadcast to all
await swarmComm.broadcastContext('Switching to security focus');

// Get messages for this agent
const messages = swarmComm.getMessages({ limit: 10, type: 'context' });
```

#### Pattern Broadcasting

```typescript
// Broadcast high-quality pattern
const pattern = await reasoningBank.searchPatterns('HNSW optimization', 1);
if (pattern[0].pattern.quality >= 0.7) {
  await swarmComm.broadcastPattern(pattern[0].pattern);
}

// Import patterns from other agents
const broadcasts = swarmComm.getPatternBroadcasts({ minQuality: 0.8 });
for (const bc of broadcasts) {
  await swarmComm.importBroadcastPattern(bc.id);
}
```

#### Consensus Guidance

```typescript
const consensus = await swarmComm.initiateConsensus(
  'Which authentication method should we use?',
  ['JWT', 'OAuth2', 'Session'],
  30000 // 30 second timeout
);

swarmComm.voteConsensus(consensus.id, 'JWT');

const guidance = swarmComm.generateConsensusGuidance(consensus.id);
```

#### Task Handoff

```typescript
const handoff = await swarmComm.initiateHandoff(
  'test-architect',
  'Write security tests for auth module',
  {
    filesModified: ['src/auth/login.ts', 'src/auth/session.ts'],
    patternsUsed: ['Use parameterized queries', 'Add rate limiting'],
    decisions: ['Chose JWT over sessions for stateless auth'],
    blockers: [],
    nextSteps: ['Write unit tests', 'Add integration tests'],
  }
);

// Agent 2: Accept handoff
swarmComm.acceptHandoff(handoff.id);
const context = swarmComm.generateHandoffContext(handoff.id);

// Agent 2: Complete handoff
swarmComm.completeHandoff(handoff.id, { testsWritten: 15 });
```

### Usage Examples

#### Example 1: Security Validation Hook

```typescript
registry.register(
  HookEvent.PreCommand,
  async (context) => {
    const command = context.command?.raw || '';

    // Block dangerous commands
    if (/rm -rf|format|drop database/i.test(command)) {
      return {
        success: false,
        abort: true,
        message: 'Command blocked by security hook',
      };
    }

    return { success: true };
  },
  HookPriority.Critical
);
```

#### Example 2: Metrics Collection Hook

```typescript
registry.register(
  HookEvent.PostToolUse,
  async (context) => {
    const toolName = context.tool?.name;
    const duration = context.duration;

    await metricsCollector.record({
      tool: toolName,
      duration,
      timestamp: context.timestamp,
    });

    return { success: true };
  },
  HookPriority.Background
);
```

#### Example 3: Intelligent Routing

```typescript
const result = await executor.execute(HookEvent.PreTask, {
  event: HookEvent.PreTask,
  timestamp: new Date(),
  task: { description: 'Implement OAuth2 authentication' },
});

console.log(result.data?.recommendedAgent);  // 'security-auditor'
console.log(result.data?.confidence);        // 0.92
```

---

## 📊 指标

### Performance Targets

| Metric | Target | Current |
|--------|--------|---------|
| Hook execution overhead | <10ms | ~5ms |
| ReasoningBank retrieval | <50ms | ~30ms |
| Pattern distillation | <100ms | ~75ms |
| Metrics calculation | <5ms | ~2ms |

---

## 🏛️ 设计

### Architecture

```
┌────────────────────────────────────────────────────────────────────┐
│                        Hooks System                                 │
├─────────────────┬─────────────────┬─────────────────┬──────────────┤
│   Hook Registry │  Hook Executor  │  MCP Tools      │ CLI Commands │
│   (registration)│  (execution)    │  (API access)   │ (user access)│
└────────┬────────┴────────┬────────┴────────┬────────┴──────┬───────┘
         │                 │                 │               │
         └────────────────┬┴─────────────────┴───────────────┘
                          │
                  ┌───────▼───────┐
                  │ ReasoningBank │
                  │   (learning)  │
                  └───────────────┘
```

### Swarm Communication Architecture

```
┌──────────────────────────────────────────────────────────────────────────┐
│                        Swarm Communication Hub                           │
├───────────────────┬──────────────────┬──────────────────┬───────────────┤
│ Agent Messaging   │ Pattern Broadcast│ Consensus Engine │ Task Handoff  │
│ (send/receive)    │ (share learning) │ (reach agreement)│ (delegation)  │
└─────────┬─────────┴────────┬─────────┴────────┬─────────┴───────┬───────┘
          │                  │                  │                 │
          └──────────────────┴────────┬─────────┴─────────────────┘
                                      │
                              ┌───────▼───────┐
                              │ ReasoningBank │
                              │   (learning)  │
                              └───────────────┘
```

### ReasoningBank Integration

The hooks system integrates with **ReasoningBank** for adaptive learning:

#### 4-Step Learning Pipeline

1. **RETRIEVE** - Top-k memory injection with MMR diversity
2. **JUDGE** - LLM-as-judge trajectory evaluation
3. **DISTILL** - Extract strategy memories from trajectories
4. **CONSOLIDATE** - Dedup, detect contradictions, prune patterns

### V2 Compatibility

V3 maintains full backward compatibility with V2 hooks:

#### V2 CLI Syntax (Supported)

```bash
npx claude-flow hooks pre-task --description "task"
npx claude-flow hooks session-restore --session-id "swarm-123"
npx claude-flow hooks post-edit --file "file.ts" --memory-key "swarm/agent/step"
npx claude-flow hooks notify --message "completed"
npx claude-flow hooks session-end --export-metrics true
```

#### V2 MCP Tools (Deprecated but Functional)

| V2 Tool | V3 Equivalent |
|---------|---------------|
| `swarm_init` | `swarm/init` |
| `agent_spawn` | `agent/spawn` |
| `task_orchestrate` | `tasks/create` |
| `memory_usage` | `memory/store`, `memory/search` |
| `neural_status` | `system/status` |
| `neural_train` | `hooks/pretrain` |

### Configuration

```typescript
const registry = createHookRegistry({
  maxHooksPerEvent: 50,
  defaultTimeout: 5000,
  enableMetrics: true,
  logLevel: 'info',
});
```

```typescript
const reasoningBank = createReasoningBank({
  maxTrajectories: 5000,
  distillationThreshold: 0.6,
  retrievalK: 5,
  mmrLambda: 0.7,
  enableAgentDB: true,
  namespace: 'hooks-learning',
});
```

---

## 📁 目录

```
v3/
├── @claude-flow/hooks/src/
│   ├── types.ts                    # Hook type definitions
│   ├── index.ts                    # Module exports
│   ├── registry/
│   │   └── index.ts                # Hook registration
│   ├── executor/
│   │   └── index.ts                # Hook execution engine
│   ├── reasoningbank/
│   │   ├── index.ts                # ReasoningBank learning engine
│   │   └── guidance-provider.ts    # Claude-visible output generator
│   ├── swarm/
│   │   └── index.ts                # Swarm communication hub
│   ├── daemons/
│   │   └── index.ts                # Background daemon processes
│   ├── statusline/
│   │   └── index.ts                # Status line generation
│   ├── cli/
│   │   └── guidance-cli.ts         # CLI commands
│   ├── bridge/
│   │   └── official-hooks-bridge.ts # Official hooks bridge
│   ├── mcp/
│   │   └── index.ts                # MCP tools
│   └── __tests__/
│       ├── reasoningbank.test.ts   # ReasoningBank tests
│       └── guidance-provider.test.ts # GuidanceProvider tests
├── @claude-flow/shared/src/hooks/
│   ├── types.ts                    # Shared hook types
│   ├── registry.ts                 # Hook registry (shared)
│   └── index.ts                    # Shared exports
├── mcp/tools/
│   ├── hooks-tools.ts              # MCP hook tools
│   └── v2-compat-tools.ts          # V2 compatibility layer
└── implementation/hooks/
    ├── README.md                   # This documentation
    ├── CLI-REFERENCE.md            # CLI command reference
    ├── MCP-TOOLS.md                # MCP tools documentation
    └── STATUSLINE-DAEMONS.md       # Daemon documentation
```

---

## 🔗 资源

### ADR References

- **ADR-005**: MCP-First API Design - Hooks exposed as MCP tools
- **ADR-006**: Unified Memory Service - ReasoningBank integration
- **ADR-007**: Event Sourcing - Hook events as audit trail

---

## 🚀 快速开始

### Testing

```bash
# Run hooks tests
cd v3/@claude-flow/shared
npm test -- hooks.test.ts

# Run with coverage
npm test -- --coverage hooks/
```

---

## ⚙️ 技术栈

「待补充」