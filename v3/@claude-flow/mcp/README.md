---
title: "@claude-flow/mcp"
tags: ["mcp", "tools", "resources", "prompts", "server", "sse", "transport", "json-rpc"]
category: "@claude-flow/mcp"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/mcp

[![npm version](https://img.shields.io/npm/v/@claude-flow/mcp.svg)](https://www.npmjs.com/package/@claude-flow/mcp)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/mcp.svg)](https://www.npmjs.com/package/@claude-flow/mcp)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-blue.svg)](https://www.typescriptlang.org/)
[![MCP](https://img.shields.io/badge/MCP-Compliant-green.svg)](https://modelcontextprotocol.io)

---

## 💡 概述

Full MCP (Model Context Protocol) server implementation for Claude Flow V3 - wire-compatible with Anthropic's `@modelcontextprotocol/sdk`, cross-account Lambda warmers, tools, resources, prompts, and lifecycle management.

---

## ⚡ 功能

- **Full MCP Specification** - Tools, resources, prompts, sampling, roots, logging
- **Transport Layer** - SSE and stdio transports
- **JSON-RPC 2.0** - Standard JSON-RPC messaging
- **Resource Management** - File resources, dynamic resources, and content types
- **Prompt Management** - Prompt templates with argument schemas
- **Tool Execution** - Declarative tools with validation and error handling
- **Lifecycle** - Initialize, heartbeat, and shutdown lifecycle
- **Server Capabilities** - Capability negotiation per MCP spec
- **Cross-Account Lambda Warmers** - Keep Lambda functions warm across AWS accounts (SSE transport)
- **Bundled Servers** - ready-to-copy-and-run example servers in `examples/`

---

## 📊 指标

### Core Endpoints

| URI | Method | Description |
|-----|--------|-------------|
| `/mcp/initialize` | POST | Initialize MCP session |
| `/mcp/tools/list` | POST | List available tools |
| `/mcp/tools/call` | POST | Execute a tool call |
| `/mcp/tools/call/:name` | POST | Execute a named tool call |
| `/mcp/resources/list` | POST | List available resources |
| `/mcp/resources/read` | POST | Read resource content |
| `/mcp/prompts/list` | POST | List available prompts |
| `/mcp/prompts/get` | POST | Get a prompt by name |
| `/mcp/sse` | GET | SSE event stream |
| `/mcp/sse-terminal` | GET | SSE stream for terminal emulation |
| `/mcp/health` | GET | Health check |
| `/mcp/lambda/warm-functions` | POST | Warm cross-account Lambda functions |
| `/mcp/lambda/list-lambdas` | GET | List managed Lambda functions |
| `/mcp/capabilities` | GET | Server capabilities |
| `/mcp/roots/list` | POST | List roots (for clients) |
| `/mcp/sampling/createMessage` | POST | LLM sampling request |
| `/mcp/logging/setLevel` | POST | Set log level |
| `/mcp/log` | POST | Send log message (server->client) |
| `/mcp/stats` | GET | Server statistics |

---

## 🏛️ 设计

### Architecture

```
HTTP Request
    │
    ▼
┌─────────────┐
│   Transport  │  SSE or stdio
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  JSON-RPC 2.0│
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  MCP Server  │
│  ┌─────────┐ │
│  │ Tools    │ │
│  ├─────────┤ │
│  │ Resources│ │
│  ├─────────┤ │
│  │ Prompts  │ │
│  └─────────┘ │
└─────────────┘
```

### Server Initialization Pattern

```typescript
// Same wire protocol as @modelcontextprotocol/sdk
interface InitializeRequest {
  jsonrpc: '2.0';
  method: 'initialize';
  params: {
    protocolVersion: string;
    capabilities: ClientCapabilities;
    clientInfo: { name: string; version: string; };
  };
  id: number;
}

interface InitializeResponse {
  jsonrpc: '2.0';
  result: {
    protocolVersion: string;
    capabilities: ServerCapabilities;
    serverInfo: { name: string; version: string; };
    instructions?: string;
  };
  id: number;
}
```

### Tool Registration

```typescript
import { MCPTool, createMCPServer } from '@claude-flow/mcp';

const tools: MCPTool[] = [
  {
    name: 'greet',
    description: 'Greet a user',
    inputSchema: {
      type: 'object',
      properties: {
        name: { type: 'string', description: 'Name to greet' }
      },
      required: ['name']
    },
    handler: async (input) => ({
      content: [{ type: 'text', text: `Hello, ${input.name}!` }]
    })
  }
];

const server = createMCPServer({
  name: 'My MCP Server',
  version: '1.0.0',
  tools
});

await server.start({ port: 3000 });
```

### Resource Management

```typescript
import { createMCPServer } from '@claude-flow/mcp';

const server = createMCPServer({
  name: 'file-server',
  version: '1.0.0',
  resources: [
    {
      uri: 'file:///project/README.md',
      name: 'Project README',
      description: 'Project documentation',
      mimeType: 'text/markdown',
      handler: async () => {
        const content = await fs.readFile('/project/README.md', 'utf-8');
        return { contents: [{ uri: 'file:///project/README.md', text: content }] };
      }
    }
  ]
});
```

### Lambda Warmers

```typescript
import { CrossAccountLambdaWarmer } from '@claude-flow/mcp';

const warmer = new CrossAccountLambdaWarmer({
  accounts: [
    {
      accountId: '123456789012',
      roleArn: 'arn:aws:iam::123456789012:role/LambdaWarmer',
      functions: ['my-function-1', 'my-function-2']
    }
  ],
  concurrency: 5,
  region: 'us-east-1'
});

// Warm all functions
await warmer.warmAll();

// Warm specific function
await warmer.warmFunction(0, 'my-function-1');

// Warm functions with specific payload
await warmer.warmAll({ type: 'keep-warm', timestamp: Date.now() });
```

### SSE Transport

```typescript
// Client-side SSE subscription
const events = new EventSource('http://localhost:3000/mcp/sse');

events.addEventListener('message', (event) => {
  const data = JSON.parse(event.data);
  console.log('MCP Event:', data);
});

// Terminal emulation SSE stream
const terminal = new EventSource('http://localhost:3000/mcp/sse-terminal');
terminal.addEventListener('data', (event) => {
  process.stdout.write(event.data); // Stream to terminal
});
```

### Bundled Example Servers

```
examples/
├── basic-server/          Minimal MCP server with one tool
├── resource-server/       Server exposing file resources
├── prompt-server/         Server with prompt templates
├── sse-server/            SSE streaming server
├── terminal-emulator/     Terminal emulation over SSE
├── lambda-warmer/         Cross-account Lambda warming
├── express-integration/   MCP server as Express middleware
├── multi-account-warmer/  Warming across multiple AWS accounts
├── full-lifecycle/        Complete MCP lifecycle implementation
└── { other examples }
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Installation

```bash
npm install @claude-flow/mcp
```

### Protocol Compliance

Wire-compatible with Anthropic's [`@modelcontextprotocol/sdk`](https://www.npmjs.com/package/@modelcontextprotocol/sdk). Any MCP client that works with the reference SDK works with this server.

### Related Packages

- [@claude-flow/plugins](../plugins) - MCP tool definitions via plugin SDK
- [@claude-flow/security](../security) - Security validation for tool inputs

### References

- [Model Context Protocol Specification](https://modelcontextprotocol.io)
- [MCP SDK (Anthropic)](https://www.npmjs.com/package/@modelcontextprotocol/sdk)

### License

MIT

---

## 🚀 快速开始

```typescript
import { createMCPServer } from '@claude-flow/mcp';

// Create a server with tools
const server = createMCPServer({
  name: 'My MCP Server',
  version: '1.0.0',
  tools: [
    {
      name: 'echo',
      description: 'Echo back the input',
      inputSchema: {
        type: 'object',
        properties: {
          message: { type: 'string', description: 'Message to echo' }
        },
        required: ['message']
      },
      handler: async (input) => ({
        content: [{ type: 'text', text: input.message }]
      })
    }
  ]
});

// Start the server
await server.start({ port: 3000 });
console.log('MCP Server running on http://localhost:3000');
```

---

## ⚙️ 技术栈

- TypeScript
- Node.js
- JSON-RPC 2.0
- SSE (Server-Sent Events)
- MCP Protocol
- AWS SDK (for Lambda warmers)