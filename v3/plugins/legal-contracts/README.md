---
title: "Legal Contracts Plugin"
tags: ["legal", "contracts", "clause-extraction", "risk-assessment", "compliance", "WASM", "MCP", "plugin", "privilege"]
category: "plugins/legal-contracts"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/plugin-legal-contracts

[![npm version](https://img.shields.io/npm/v/@claude-flow/plugin-legal-contracts.svg)](https://www.npmjs.com/package/@claude-flow/plugin-legal-contracts)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/plugin-legal-contracts.svg)](https://www.npmjs.com/package/@claude-flow/plugin-legal-contracts)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 💡 概述

A comprehensive legal contract analysis plugin combining hyperbolic embeddings for legal ontology navigation with fast vector search for clause similarity. The plugin enables automated clause extraction, risk scoring, obligation tracking, and regulatory compliance checking while maintaining attorney-client privilege through on-device processing.

---

## ⚡ 功能

- **Clause Extraction**: Extract and classify clauses from contracts (indemnification, liability, termination, etc.)
- **Risk Assessment**: Identify and score contractual risks by category and severity
- **Contract Comparison**: Compare contracts with detailed diff and semantic alignment
- **Obligation Tracking**: Extract obligations, deadlines, and dependencies using DAG analysis
- **Playbook Matching**: Compare contract clauses against negotiation playbook positions

### MCP Tools

#### 1. `legal/clause-extract`

```typescript
const result = await mcp.invoke('legal/clause-extract', {
  document: contractText,
  clauseTypes: [
    'indemnification',
    'limitation_of_liability',
    'termination',
    'confidentiality',
    'ip_assignment',
    'governing_law'
  ],
  jurisdiction: 'US',
  includePositions: true
});
```

#### 2. `legal/risk-assess`

```typescript
const result = await mcp.invoke('legal/risk-assess', {
  document: contractText,
  partyRole: 'licensee',
  riskCategories: ['financial', 'legal', 'compliance'],
  industryContext: 'software',
  threshold: 'medium'
});
```

#### 3. `legal/contract-compare`

```typescript
const result = await mcp.invoke('legal/contract-compare', {
  baseDocument: masterServiceAgreement,
  compareDocument: vendorRedlinedVersion,
  comparisonMode: 'full',
  highlightChanges: true,
  generateRedline: true
});
```

#### 4. `legal/obligation-track`

```typescript
const result = await mcp.invoke('legal/obligation-track', {
  document: contractText,
  party: 'Vendor',
  timeframe: 'P90D',
  obligationTypes: ['payment', 'delivery', 'notification'],
  includeDependencies: true
});
```

#### 5. `legal/playbook-match`

```typescript
const result = await mcp.invoke('legal/playbook-match', {
  document: vendorContract,
  playbook: 'enterprise-saas-v2',
  strictness: 'moderate',
  suggestAlternatives: true,
  prioritizeClauses: ['indemnification', 'limitation_of_liability']
});
```

---

## 📊 指标

| Metric | Target |
|--------|--------|
| Clause extraction | <2s for 50-page contract |
| Risk assessment | <5s full analysis |
| Contract comparison | <10s for two 100-page contracts |
| Obligation extraction | <3s per contract |
| Playbook matching | <1s per clause |

### Rate Limiting

```typescript
const rateLimits = {
  'legal/clause-extract': { requestsPerMinute: 30, maxConcurrent: 3 },
  'legal/risk-assess': { requestsPerMinute: 20, maxConcurrent: 2 },
  'legal/contract-compare': { requestsPerMinute: 10, maxConcurrent: 1 },
  'legal/obligation-track': { requestsPerMinute: 30, maxConcurrent: 3 },
  'legal/playbook-match': { requestsPerMinute: 30, maxConcurrent: 3 }
};
```

---

## 🏛️ 设计

### Configuration Options

```typescript
interface LegalContractsConfig {
  indexPath: string;              // Path to clause index storage
  playbookPath: string;           // Path to playbook definitions
  privilegeProtection: boolean;   // Zero-knowledge processing (default: true)
  auditLogPath: string;           // Path for ethics-compliant audit logs
  maxMemoryMB: number;            // WASM memory limit (default: 2048)
  maxCpuTimeSeconds: number;      // Operation timeout (default: 120)
  matterIsolation: boolean;       // Per-matter WASM instances (default: true)
  roleBasedAccess: boolean;       // Enable RBAC (default: true)
  ethicalWalls: boolean;          // Enable conflict checking (default: true)
}
```

### Security Considerations

#### Attorney-Client Privilege Protection

| Requirement | Implementation |
|-------------|----------------|
| **Privilege Preservation** | Zero-knowledge processing - no document content transmitted |
| **Work Product Protection** | Analysis results encrypted, attorney-eyes-only by default |
| **Document Isolation** | Each matter processed in isolated WASM instance |
| **Chain of Custody** | Cryptographic proof of document handling |
| **Conflict Detection** | Prevent cross-matter data leakage |

#### Matter Isolation

Each legal matter gets its own isolated WASM instance:
- Separate memory regions per matter
- No cross-matter data access possible
- Independent audit trails per matter
- Automatic cleanup on matter close

#### Role-Based Access Control

```typescript
const roles = {
  PARTNER: ['clause-extract', 'risk-assess', 'contract-compare', 'obligation-track', 'playbook-match'],
  ASSOCIATE: ['clause-extract', 'risk-assess', 'contract-compare', 'obligation-track'],
  PARALEGAL: ['clause-extract', 'obligation-track'],
  CONTRACT_MANAGER: ['obligation-track', 'playbook-match'],
  CLIENT: []  // No direct tool access - results only via attorney
};
```

#### Ethical Walls

```typescript
interface EthicalWall {
  matterId: string;
  blockedUsers: string[];     // Users who cannot access this matter
  reason: string;             // Encrypted conflict reason
  createdBy: string;          // Partner who created wall
  createdAt: string;
}
```

#### Audit Logging

All tool invocations are logged with:
- Timestamp (ISO 8601)
- User ID and role at time of access
- Matter ID
- Document hash (not content)
- Operation type

Logs are encrypted at rest, accessible only to ethics/compliance, and retained per jurisdiction requirements (typically 7+ years).

#### Input Validation

All inputs are validated using Zod schemas:
- Maximum document size: 10MB
- Maximum clause types per request: 20
- Path traversal prevention on file inputs
- Malicious content detection (macros, scripts)
- Character encoding validation (UTF-8 required)
- Playbook identifiers: alphanumeric with hyphens, max 64 characters

#### WASM Security Constraints

| Constraint | Value | Rationale |
|------------|-------|-----------|
| Memory Limit | 2GB max | Handle large contract documents |
| CPU Time Limit | 120 seconds | Allow complex multi-document comparison |
| No Network Access | Enforced | Preserve attorney-client privilege |
| No File System Access | Sandboxed paths only | Prevent unauthorized document access |
| Per-Matter Isolation | Enforced | Prevent cross-matter data leakage |

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Related Plugins

| Plugin | Description | Use Case |
|--------|-------------|----------|
| [@claude-flow/plugin-financial-risk](../financial-risk) | Financial risk analysis | Financial contract risk assessment |
| [@claude-flow/plugin-healthcare-clinical](../healthcare-clinical) | Clinical decision support | Healthcare BAA and compliance agreements |
| [@claude-flow/plugin-code-intelligence](../code-intelligence) | Code analysis | Software licensing and IP contracts |

---

## 🚀 快速开始

### Installation

#### npm

```bash
npm install @claude-flow/plugin-legal-contracts
```

#### CLI

```bash
npx claude-flow plugins install --name @claude-flow/plugin-legal-contracts
```

### Quick Start

```typescript
import { LegalContractsPlugin } from '@claude-flow/plugin-legal-contracts';

// Initialize the plugin
const legal = new LegalContractsPlugin({
  indexPath: './data/clause-index',
  playbookPath: './data/playbooks',
  privilegeProtection: true
});

// Extract clauses from a contract
const clauses = await legal.clauseExtract({
  document: contractText,
  clauseTypes: ['indemnification', 'limitation_of_liability', 'termination'],
  jurisdiction: 'US',
  includePositions: true
});

// Assess contract risks
const risks = await legal.riskAssess({
  document: contractText,
  partyRole: 'buyer',
  riskCategories: ['financial', 'legal', 'operational'],
  threshold: 'medium'
});

// Compare two contracts
const comparison = await legal.contractCompare({
  baseDocument: standardContract,
  compareDocument: vendorContract,
  comparisonMode: 'full',
  generateRedline: true
});
```

---

## ⚙️ 技术栈

- `micro-hnsw-wasm`: Fast semantic search for clause similarity and precedent matching
- `ruvector-hyperbolic-hnsw-wasm`: Legal taxonomy navigation (contract types, clause hierarchies)
- `ruvector-attention-wasm`: Cross-attention for contract comparison (redline analysis)
- `ruvector-dag-wasm`: Contract dependency graphs (obligations, conditions, timelines)
- `pdf-parse`: PDF document parsing
- `mammoth`: DOCX document parsing

## License

MIT License

Copyright (c) 2026 Claude Flow

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.