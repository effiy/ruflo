---
title: "@claude-flow/plugin-teammate"
tags: ["teammate", "multi-agent", "coordination", "collaboration", "swarm", "agent-teams"]
category: "plugins/teammate"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/plugin-teammate

[![npm version](https://img.shields.io/npm/v/@claude-flow/teammate-plugin.svg)](https://www.npmjs.com/package/@claude-flow/teammate-plugin)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/teammate-plugin.svg)](https://www.npmjs.com/package/@claude-flow/teammate-plugin)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 💡 概述

Multi-agent teammate coordination plugin for Claude Flow V3 - named agent teams, real-time communication via SendMessage, pipeline and fan-out coordination patterns, task delegation, and shared memory coordination.

---

## ⚡ 功能

- **Named Agent Teams** - Addressable agents with SendMessage communication
- **Pipeline Coordination** - A to B to C sequential workflows
- **Fan-out / Fan-in** - Parallel worker spawning with result collection
- **Supervisor / Worker** - Central lead with worker delegation
- **Task Delegation** - Assign, redirect, and track tasks across agents
- **Shared Memory** - Cross-agent memory with namespace isolation
- **Graceful Shutdown** - Agent lifecycle management
- **Comms Protocol** - Standardized inter-agent messaging format

### Coordination Patterns

| Pattern | Description | Use Case |
|---------|-------------|----------|
| **Pipeline** | A -> B -> C sequential | Each step dependent on previous |
| **Fan-out** | Lead spawns N workers in parallel | Independent subtasks |
| **Supervisor** | Lead delegates, workers report | Ongoing team coordination |
| **Hybrid** | Mix of patterns | Complex multi-phase workflows |

---

## 📊 指标

| Metric | Target |
|--------|--------|
| Message latency | <100ms |
| Agent spawn time | <500ms |
| Memory read/write | <10ms |

---

## 🏛️ 设计

### Named Agent Teams + SendMessage

Named agents communicate in real-time via `SendMessage`. Every agent MUST have a `name` for addressability.

```typescript
// Spawn named agents
import { AgentTeam } from '@claude-flow/teammate-plugin';

const team = new AgentTeam({
  name: 'feature-team',
  agents: [
    { name: 'architect', type: 'system-architect' },
    { name: 'developer', type: 'coder' },
    { name: 'tester', type: 'tester' },
    { name: 'security', type: 'security-auditor' }
  ]
});

await team.initialize();
```

### Pipeline Pattern

```typescript
import { PipelineCoordination } from '@claude-flow/teammate-plugin';

const pipeline = new PipelineCoordination({
  agents: ['architect', 'developer', 'tester'],
  autoProceed: true
});

// Start pipeline
await pipeline.start({
  architect: {
    task: 'Design the database schema',
    deliverTo: 'developer'
  },
  developer: {
    task: 'Implement based on design',
    deliverTo: 'tester'
  },
  tester: {
    task: 'Write and run tests'
  }
});

// Monitor progress
const status = await pipeline.getStatus();
console.log(status.progress); // { architect: 'done', developer: 'running', tester: 'waiting' }
```

### Fan-out / Fan-in

```typescript
import { FanOutCoordination } from '@claude-flow/teammate-plugin';

const fanOut = new FanOutCoordination({
  workers: ['researcher-1', 'researcher-2', 'researcher-3'],
  collector: 'lead'
});

// Fan out tasks
const results = await fanOut.distribute({
  'researcher-1': 'Analyze auth patterns',
  'researcher-2': 'Analyze data patterns',
  'researcher-3': 'Analyze API patterns'
});

// Collect and synthesize
const synthesis = await fanOut.synthesize(results);
```

### Supervisor / Worker Pattern

```typescript
import { SupervisorCoordination } from '@claude-flow/teammate-plugin';

const supervisor = new SupervisorCoordination({
  supervisor: 'lead',
  workers: ['coder-1', 'coder-2', 'reviewer']
});

// Assign tasks
await supervisor.assign('coder-1', {
  task: 'Implement auth module',
  priority: 'high'
});

await supervisor.assign('coder-2', {
  task: 'Implement data layer',
  priority: 'medium'
});

// Reassign priorities
await supervisor.reprioritize('coder-1', 'critical');

// Check worker status
const workers = await supervisor.getWorkers();
```

### Shared Memory Coordination

```typescript
import { TeammateMemory } from '@claude-flow/teammate-plugin';

const memory = new TeammateMemory({
  namespace: 'feature-xyz',
  ttl: 3600
});

// Store artifacts
await memory.store('design-decisions', {
  pattern: 'Repository',
  stack: { db: 'PostgreSQL', cache: 'Redis' }
});

// Retrieve in another agent
const decisions = await memory.retrieve('design-decisions');

// Search across team knowledge
const patterns = await memory.search('authentication');
```

### Agent Prompt Template

```typescript
import { AgentTemplate } from '@claude-flow/teammate-plugin';

const prompt = AgentTemplate.build({
  agentName: 'architect',
  task: 'Design the database schema',
  comms: {
    deliverTo: 'developer',
    onError: 'lead',
    urgentTo: 'lead'
  },
  deliverable: 'Schema design with entities, indexes, migration plan'
});
```

### Lifecycle Hooks

```typescript
const team = new AgentTeam({ ... });

team.on('agent:spawned', ({ agentName, processId }) => {
  console.log(`Agent ${agentName} spawned with PID ${processId}`);
});

team.on('agent:completed', ({ agentName, result }) => {
  console.log(`Agent ${agentName} completed:`, result.summary);
});

team.on('agent:error', ({ agentName, error }) => {
  console.error(`Agent ${agentName} error:`, error.message);
});

team.on('team:complete', ({ results, duration }) => {
  console.log(`Team completed in ${duration}ms`);
});

team.on('message:received', ({ from, to, summary }) => {
  console.log(`Message from ${from} to ${to}: ${summary}`);
});
```

### Graceful Shutdown

```typescript
const team = new AgentTeam({ ... });

// Request shutdown
await team.shutdown({
  reason: 'work complete',
  timeout: 30000,
  forceAfter: false
});

// Force shutdown
await team.shutdown({
  reason: 'emergency',
  timeout: 5000,
  forceAfter: true
});
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Installation

```bash
npx claude-flow@v3alpha plugins install @claude-flow/teammate-plugin
```

### Quick Commands

```bash
# List team agents
npx claude-flow@v3alpha agent list

# Spawn a named agent
npx claude-flow@v3alpha agent spawn -t coder --name my-coder

# Send message to agent
npx claude-flow@v3alpha agent message --to my-coder --summary "Start task"
```

### Rules

1. **Always name agents** - use `name: "role-name"` so they're addressable
2. **Comms over memory** - use SendMessage for real-time, memory for persistence
3. **Pipeline prompts** - tell each agent WHO to message next
4. **Spawn all at once** - all Task calls in ONE message with `run_in_background: true`
5. **Don't poll** - agents message back when done; wait for completion notifications
6. **Graceful shutdown** - send `{ type: "shutdown_request" }` before terminating
7. **Lead synthesizes** - review ALL results before responding to user

### Related Packages

- [@claude-flow/swarm](../../@claude-flow/swarm) - Swarm orchestration
- [@claude-flow/memory](../../@claude-flow/memory) - AgentDB memory

### License

MIT

---

## 🚀 快速开始

```typescript
import { AgentTeam, PipelineCoordination } from '@claude-flow/teammate-plugin';

// Create team
const team = new AgentTeam({
  name: 'feature-team',
  agents: [
    { name: 'architect', type: 'system-architect' },
    { name: 'developer', type: 'coder' },
    { name: 'tester', type: 'tester' }
  ]
});

await team.initialize();

// Run pipeline: architect -> developer -> tester
const pipeline = new PipelineCoordination({ agents: team.names });
await pipeline.start({
  architect: { task: 'Design API', deliverTo: 'developer' },
  developer: { task: 'Implement API', deliverTo: 'tester' },
  tester: { task: 'Test API' }
});

const results = await pipeline.waitForCompletion();
console.log('Pipeline complete:', results.summary);

await team.shutdown();
```

---

## ⚙️ 技术栈

- TypeScript
- Node.js
- AgentDB shared memory
- MCP protocol