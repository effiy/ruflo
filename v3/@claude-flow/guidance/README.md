---
title: "@claude-flow/guidance"
tags: ["guidance", "governance", "control-plane", "wasm", "policy", "dsl", "analyzer", "compliance"]
category: "@claude-flow/guidance"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/guidance

[![npm version](https://img.shields.io/npm/v/@claude-flow/guidance.svg)](https://www.npmjs.com/package/@claude-flow/guidance)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/guidance.svg)](https://www.npmjs.com/package/@claude-flow/guidance)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-blue.svg)](https://www.typescriptlang.org/)
[![Governance](https://img.shields.io/badge/Governance-Control%20Plane-purple.svg)](https://github.com/ruvnet/claude-flow)

---

## 💡 概述

Governance control plane for Claude Flow V3 -- policy-driven behavioral guidance with 31 modules. Compiles governance DSL into executable policy kernels, enforces constraints at the system boundary, proves compliance, and evolves policies through Darwin-loop learning.

---

## ⚡ 功能

### 31 Modules

#### Policy & Governance

| Module | Purpose |
|--------|---------|
| `policy-compiler` | Compile governance DSL into executable WASM kernels |
| `constraint-enforcer` | Enforce policies at system boundaries (access, resources, network, data) |
| `compliance-prover` | Formal verification of policy compliance (SAT/SMT-based) |
| `governance-dsl` | Domain-specific language for policy definition |
| `policy-evolver` | Darwin-loop evolutionary policy optimization |
| `audit-trail` | Immutable audit logging with chain-of-custody |

#### Security

| Module | Purpose |
|--------|---------|
| `security-policy` | Security posture and threat model policies |
| `access-control` | RBAC, ABAC, and claims-based access control |
| `network-policy` | Network segmentation and firewall rules |
| `data-governance` | Data classification, retention, and sovereignty |
| `secret-management` | Credential lifecycle and rotation policies |
| `crypto-policy` | Cryptographic algorithm and key-length policies |

#### Operations

| Module | Purpose |
|--------|---------|
| `resource-governance` | CPU, memory, disk, and network quotas |
| `rate-limit-policy` | API rate limiting and throttling rules |
| `cost-governance` | Spending limits and cost allocation policies |
| `deployment-policy` | Canary, blue-green, and rollout strategies |
| `incident-response` | Automated incident detection and response |

#### Quality & Testing

| Module | Purpose |
|--------|---------|
| `quality-gates` | Code quality, coverage, and performance thresholds |
| `testing-policy` | Test strategy, coverage requirements, and approval gates |
| `benchmark-policy` | Performance benchmark requirements and regression gates |

#### AI & Model Governance

| Module | Purpose |
|--------|---------|
| `model-governance` | Model approval, versioning, and deprecation |
| `prompt-governance` | Prompt templates, safety filters, and content policies |
| `agent-governance` | Agent lifecycle, autonomy limits, and capability boundaries |
| `learning-policy` | Training data, fine-tuning, and pattern-learning policies |

#### Infrastructure

| Module | Purpose |
|--------|---------|
| `infrastructure-policy` | Terraform/CloudFormation governance |
| `container-policy` | Docker/Kubernetes security and resource policies |
| `storage-policy` | Storage class, encryption, and retention policies |

#### Custom & Extension

| Module | Purpose |
|--------|---------|
| `custom-policy` | User-defined policy extensions |
| `policy-templates` | Pre-built policy templates for common scenarios |
| `policy-marketplace` | Community-contributed policies |
| `policy-simulator` | "What-if" policy impact simulation |
| `policy-reporter` | Compliance dashboards and reporting |

### Governance DSL

```yaml
# governance.yaml
version: "3.0"
name: "production-compliance"

policies:
  - id: "auth-required"
    description: "All agents must authenticate"
    applies_to: ["agent:spawn", "tool:execute"]
    rules:
      - type: "require_auth"
        level: "critical"

  - id: "rate-limit"
    description: "API rate limiting"
    applies_to: ["api:request"]
    rules:
      - type: "rate_limit"
        max_per_minute: 100
        burst: 200

  - id: "data-sovereignty"
    description: "Data must stay in region"
    applies_to: ["data:store", "data:transfer"]
    rules:
      - type: "geo_fence"
        regions: ["us-east-1", "us-west-2"]

constraints:
  max_concurrent_agents: 10
  max_memory_per_agent_mb: 512
  max_execution_time_ms: 30000
  allowed_providers: ["anthropic", "openai"]
  require_signed_commits: true
  require_code_review: true
  require_security_scan: true
```

---

## 📊 指标

### Compilation Targets

| Target | Description | Performance |
|--------|-------------|-------------|
| **WASM** | WebAssembly kernel (fastest) | <1ms policy check |
| **JS** | Native JavaScript (fallback) | <2ms policy check |
| **DSL Interpreted** | Direct DSL interpretation (debug) | <5ms policy check |

### Policy Statistics

| Metric | Default |
|--------|---------|
| Policy compile time | <50ms |
| WASM kernel warmup | <10ms |
| Constraint check latency | <1ms |
| Audit trail throughput | 10,000+ entries/sec |

---

## 🏛️ 设计

### Quick Start -- Load and enforce a governance policy

```typescript
import { GuidanceBrain, createGovernance } from '@claude-flow/guidance';

const brain = await createGovernance('./governance.yaml');

// Check if an action is allowed
const decision = await brain.recommend({
  task: 'Spawn a coder agent',
  context: { agentCount: 5, memoryUsageMB: 200 }
});

console.log(decision.allowed);    // true/false
console.log(decision.reasoning);  // Human-readable explanation
console.log(decision.constraints); // Applicable constraints
```

### Governance Brain API

```typescript
import { GuidanceBrain } from '@claude-flow/guidance';

const brain = new GuidanceBrain({
  policyPath: './governance.yaml',
  target: 'wasm',       // 'wasm' | 'js' | 'dsl'
  mode: 'enforce',      // 'recommend' | 'enforce' | 'audit'
  auditLog: true
});

// Initialize
await brain.initialize();

// Get recommendation
const rec = await brain.recommend({
  mode: 'recommend',
  task: 'Deploy to production'
});
console.log(rec.recommendation); // 'deny' | 'allow' | 'warn'

// Enforce policy (throws if violated)
try {
  await brain.enforce({
    action: 'agent:spawn',
    params: { type: 'coder', count: 1 }
  });
} catch (err) {
  console.error('Policy violation:', err.message);
}

// Audit mode (logs but doesn't block)
await brain.audit({
  action: 'file:write',
  params: { path: '/etc/config', content: '...' }
});

// Health check
const health = await brain.health();
// { status: 'healthy', policiesLoaded: 15, lastCompiled: '2026-01-01T00:00:00Z' }
```

### Policy Compiler

```typescript
import { PolicyCompiler } from '@claude-flow/guidance';

const compiler = new PolicyCompiler();

// Compile DSL to WASM
const wasm = await compiler.compile('./governance.yaml', {
  target: 'wasm',
  optimize: true
});

// Compile to JavaScript
const js = await compiler.compile('./governance.yaml', {
  target: 'js'
});

// Validate DSL
const errors = await compiler.validate('./governance.yaml');
if (errors.length > 0) {
  console.error('Policy errors:', errors);
}
```

### Constraint Enforcer

```typescript
import { ConstraintEnforcer } from '@claude-flow/guidance';

const enforcer = new ConstraintEnforcer(policy);

// Enforce access constraints
const access = await enforcer.checkAccess({
  subject: 'agent:coder-1',
  resource: 'file:/etc/config',
  action: 'write'
});

// Enforce resource constraints
const resources = await enforcer.checkResources({
  agentCount: 5,
  memoryUsageMB: 300
});

// Enforce network constraints
const network = await enforcer.checkNetwork({
  source: 'agent:coder-1',
  destination: 'api.external.com',
  port: 443
});
```

### Compliance Prover

```typescript
import { ComplianceProver } from '@claude-flow/guidance';

const prover = new ComplianceProver(policy);

// Prove compliance
const result = await prover.prove({
  standards: ['SOC2', 'HIPAA', 'ISO27001'],
  evidence: './compliance/evidence.json'
});

// Generate compliance report
const report = await prover.generateReport({
  format: 'pdf',
  standards: ['SOC2'],
  period: { start: '2026-01-01', end: '2026-12-31' }
});
```

### Audit Trail

```typescript
import { AuditTrail } from '@claude-flow/guidance';

const audit = new AuditTrail({
  storage: 'immutable',
  chain: true  // Chain-of-custody verification
});

// Log audit event
await audit.record({
  action: 'agent:spawn',
  subject: 'user:admin',
  resource: 'agent:coder-1',
  result: 'allowed',
  policy: 'auth-required',
  timestamp: Date.now()
});

// Query audit trail
const events = await audit.query({
  action: 'agent:spawn',
  from: Date.now() - 86400000,
  to: Date.now()
});

// Verify chain integrity
const integrity = await audit.verifyChain();
console.log(integrity.valid);    // true
console.log(integrity.gaps);     // [] if no tampering
```

### Policy Evolver (Darwin Loop)

```typescript
import { PolicyEvolver } from '@claude-flow/guidance';

const evolver = new PolicyEvolver({
  population: 100,
  generations: 50,
  fitness: 'balanced',
  mutation: 0.1
});

// Evolve policies
const evolved = await evolver.evolve({
  seedPolicy: './governance.yaml',
  trainingData: './audit/training.json',
  constraints: {
    safety: 0.95,
    performance: 0.85
  }
});

console.log(`Best fitness: ${evolved.fitness}`);
console.log(`Generations: ${evolved.generations}`);

// Apply evolved policy (requires approval)
await evolver.apply(evolved.policy, {
  requireApproval: true,
  backup: true
});
```

### Governance DSL

```typescript
import { GovernanceDSL } from '@claude-flow/guidance';

const dsl = new GovernanceDSL();

// Parse and validate
const policy = await dsl.parse('./governance.yaml');

// Programmatic policy creation
const newPolicy = dsl.create()
  .policy('rate-limit')
    .description('API rate limiting')
    .appliesTo('api:request')
    .rule('rate_limit', { max_per_minute: 100 })
  .policy('data-sovereignty')
    .description('Data must stay in region')
    .appliesTo('data:store')
    .rule('geo_fence', { regions: ['us-east-1'] })
  .build();

// Export
await dsl.export(newPolicy, './governance-optimized.yaml');
```

### Access Control

```typescript
import { AccessControl } from '@claude-flow/guidance';

const ac = new AccessControl({
  model: 'abac',  // 'rbac' | 'abac' | 'hybrid'
  roles: {
    admin: { permissions: ['*'] },
    developer: { permissions: ['read', 'write', 'execute'] },
    viewer: { permissions: ['read'] }
  }
});

// Check permission
const allowed = await ac.check({
  subject: 'user:john',
  action: 'write',
  resource: 'file:src/index.ts',
  context: { environment: 'development' }
});

// Grant role
await ac.grantRole('user:john', 'developer');

// Revoke role
await ac.revokeRole('user:john', 'viewer');
```

### Policy Templates

```typescript
import { PolicyTemplates } from '@claude-flow/guidance';

const templates = new PolicyTemplates();

// List available templates
const available = templates.list();
// ['soc2-baseline', 'hipaa-healthcare', 'pci-dss', 'gdpr-privacy', ...]

// Apply template
const policy = await templates.apply('soc2-baseline', {
  environment: 'production',
  teamSize: 10
});
```

### Policy Simulation

```typescript
import { PolicySimulator } from '@claude-flow/guidance';

const sim = new PolicySimulator();

// Run "what-if" simulation
const impact = await sim.simulate({
  policy: './governance.yaml',
  scenario: 'data-breach',
  duration: '30d'
});

console.log(`Impact: ${impact.summary}`);
console.log(`Blocked events: ${impact.blocked}`);
console.log(`False positives: ${impact.falsePositives}`);
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Installation

```bash
npm install @claude-flow/guidance
```

### CLI Integration

```bash
# Compile and enforce governance
npx claude-flow@v3alpha guidance enforce --policy ./governance.yaml

# Recommend action
npx claude-flow@v3alpha guidance recommend --task "Spawn coder agent"

# Audit compliance
npx claude-flow@v3alpha guidance audit --standard SOC2

# Generate report
npx claude-flow@v3alpha guidance report --format pdf --standard SOC2
```

### Environment Variables

```bash
GUIDANCE_POLICY_PATH=./governance.yaml
GUIDANCE_MODE=enforce
GUIDANCE_TARGET=wasm
GUIDANCE_AUDIT_LOG=true
```

### Related Packages

- [@claude-flow/cli](../cli) - CLI with guidance integration
- [@claude-flow/security](../security) - Security policy enforcement
- [@claude-flow/aidefence](../aidefence) - AI manipulation defense governed by guidance

### License

MIT

---

## 🚀 快速开始

```typescript
import { createGovernance } from '@claude-flow/guidance';

// Minimal governance setup
const governance = await createGovernance({
  constraints: {
    max_concurrent_agents: 10,
    require_code_review: true,
    require_security_scan: true
  }
});

// Check before action
const decision = await governance.recommend({
  task: 'Deploy to production',
  context: { environment: 'production' }
});

if (decision.allowed) {
  console.log('Proceed:', decision.reasoning);
} else {
  console.log('Blocked:', decision.reasoning);
}
```

---

## ⚙️ 技术栈

- TypeScript
- Node.js
- WebAssembly (WASM policy kernels)
- Governance DSL (YAML-based)
- SAT/SMT solvers (compliance proving)
- Immutable audit logging