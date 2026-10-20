---
title: "@claude-flow/providers"
tags: ["providers", "llm", "anthropic", "openai", "models", "api", "streaming", "fallback", "cost-optimization"]
category: "@claude-flow/providers"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/providers

[![npm version](https://img.shields.io/npm/v/@claude-flow/providers.svg)](https://www.npmjs.com/package/@claude-flow/providers)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/providers.svg)](https://www.npmjs.com/package/@claude-flow/providers)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-blue.svg)](https://www.typescriptlang.org/)
[![Providers](https://img.shields.io/badge/Providers-15+-green.svg)](https://github.com/ruvnet/claude-flow)

---

## 💡 概述

Multi-LLM provider system for Claude Flow V3 - unified interface across 15+ LLM providers with model registry, streaming, fallback chains, cost optimization, and capability-aware routing.

---

## ⚡ 功能

- **15+ LLM Providers** - Unified interface for major LLM APIs
- **Model Registry** - Comprehensive model catalog with capabilities and constraints
- **Streaming** - SSE and async iterator streaming across all providers
- **Chat Completion** - Standardized chat completion API
- **Embedding Models** - Text embedding support
- **Fallback Chains** - Automatic provider fallback on failure
- **Cost Optimization** - Provider routing based on cost constraints
- **Capability Routing** - Model selection based on required capabilities (vision, function calling, etc.)
- **Rate Limiting** - Built-in rate limit handling with retries
- **Provider Discovery** - Auto-detect available providers from API keys in environment

### Supported Providers

| Provider | Models | Streaming | Embeddings | Vision | Function Calling |
|----------|--------|-----------|------------|--------|-----------------|
| **Anthropic** | Claude 3.5/3/Opus/Sonnet/Haiku | Yes | No | Yes | Yes |
| **OpenAI** | GPT-4o/GPT-4/GPT-3.5/o1/o3 | Yes | Yes | Yes | Yes |
| **Google** | Gemini 2.0/1.5/Pro/Flash | Yes | Yes | Yes | Yes |
| **Azure OpenAI** | GPT-4/3.5 deployments | Yes | Yes | Yes | Yes |
| **AWS Bedrock** | Claude, Llama, Titan | Yes | Yes | Partial | Yes |
| **Groq** | Llama/Mixtral variants | Yes | No | No | Yes |
| **Together AI** | 100+ open models | Yes | Yes | No | Partial |
| **Fireworks** | 50+ models | Yes | Yes | No | Yes |
| **DeepSeek** | DeepSeek-V3/R1 | Yes | No | No | No |
| **Mistral** | Mistral Large/Medium/Small | Yes | Yes | No | Yes |
| **Cohere** | Command R/R+ | Yes | Yes | No | Yes |
| **Replicate** | Any model on Replicate | Partial | No | Partial | No |
| **OpenRouter** | Unified API gateway | Yes | No | Partial | Partial |
| **xAI** | Grok-2 | Yes | No | No | No |
| **Custom** | OpenAI-compatible endpoints | Yes | Partial | Partial | Partial |

---

## 📊 指标

### Rate Limiting (built-in)

| Provider | Default RPM | Default TPM |
|----------|-------------|-------------|
| Anthropic | 4000 | 800,000 |
| OpenAI | 3500 | 900,000 |
| Google | 1500 | 150,000 |
| Groq | 30 | 30,000 |

### Capability Matrix

```typescript
interface ModelCapabilities {
  supportsStreaming: boolean;
  supportsFunctionCalling: boolean;
  supportsVision: boolean;
  supportsJSON: boolean;
  maxTokens: number;
  maxContextLength: number;
  multimodalInput: boolean;
  estimatedCostPer1K: { input: number; output: number; };
}
```

---

## 🏛️ 设计

### API

#### Provider Registry

```typescript
import { ProviderRegistry, getDefaultProviderRegistry } from '@claude-flow/providers';

const registry = getDefaultProviderRegistry();

// List active providers
const providers = await registry.listProviders();
// [{ name: 'anthropic', models: [...], status: 'active' }, ...]

// Execute with automatic provider selection
const response = await registry.chat({
  messages: [{ role: 'user', content: 'Hello!' }],
  capabilities: { supportsVision: true },  // Auto-select provider with vision
  maxCost: 0.02  // Budget constraint
});
```

#### Chat Completion

```typescript
import { chat, createChatClient } from '@claude-flow/providers';

// Simple one-shot chat
const result = await chat({
  provider: 'anthropic',
  model: 'claude-sonnet-4-6',
  messages: [{ role: 'user', content: 'What is TypeScript?' }],
  maxTokens: 1000
});

// Create a reusable client
const client = createChatClient({
  provider: 'openai',
  model: 'gpt-4o',
  system: 'You are a helpful assistant.'
});

const response = await client.chat([{ role: 'user', content: 'Hello!' }]);
```

#### Streaming

```typescript
import { streamChat, createStreamClient } from '@claude-flow/providers';

// Stream via async iterator
const stream = await streamChat({
  provider: 'anthropic',
  model: 'claude-opus-4-1',
  messages: [{ role: 'user', content: 'Write a poem about code' }],
  stream: true
});

for await (const chunk of stream) {
  process.stdout.write(chunk.content);
}

// SSE stream
const sseStream = await createSSEStream({
  provider: 'openai',
  model: 'gpt-4o',
  messages: [{ role: 'user', content: 'Hello!' }],
  onChunk: (chunk) => console.log(chunk),
  onComplete: () => console.log('Done')
});
```

#### Embeddings

```typescript
import { embed, batchEmbed } from '@claude-flow/providers';

// Single embedding
const vector = await embed({
  provider: 'openai',
  model: 'text-embedding-3-small',
  input: 'Hello world'
});
// { embeddings: [0.001, 0.003, ...], dimensions: 1536 }

// Batch embedding
const vectors = await batchEmbed({
  provider: 'openai',
  model: 'text-embedding-3-small',
  inputs: ['text 1', 'text 2', 'text 3']
});
// { embeddings: [[...], [...], [...]], dimensions: 1536 }
```

#### Fallback Chains

```typescript
import { createFallbackChain } from '@claude-flow/providers';

const chain = createFallbackChain({
  providers: ['anthropic', 'openai', 'google'],
  failureCriteria: {
    maxRetries: 3,
    retryDelay: 1000,
    timeout: 30000
  }
});

const response = await chain.chat({
  messages: [{ role: 'user', content: 'Hello!' }]
});
// Tries Anthropic -> falls back to OpenAI -> falls back to Google
```

#### Model Registry

```typescript
import { ModelRegistry } from '@claude-flow/providers';

const registry = new ModelRegistry();

// Find models by capability
const visionModels = registry.findModelsByCapability('vision');
// [{ provider: 'anthropic', model: 'claude-opus-4-1', ... }, ...]

// Get model info
const info = registry.getModel('claude-sonnet-4-6');
// { provider: 'anthropic', maxTokens: 8192, supportsVision: true, ... }

// Find cheapest model
const cheapest = registry.findCheapestModel({ minQualityThreshold: 0.8 });

// Model comparison
registry.compareModels(['gpt-4o', 'claude-opus-4-1', 'gemini-2.0-pro']);
```

#### Provider Discovery

```typescript
import { discoverProviders } from '@claude-flow/providers';

// Auto-detect providers from environment variables
const providers = discoverProviders();
console.log(providers);
// ['anthropic', 'openai'] (based on ANTHROPIC_API_KEY, OPENAI_API_KEY in env)

// Check available providers
const active = await getActiveProviders();
```

#### Capability Routing

```typescript
import { smartRoute } from '@claude-flow/providers';

const response = await smartRoute({
  messages: [{ role: 'user', content: 'Describe this image', images: [base64Image] }],
  constraints: {
    requiredCapabilities: ['vision', 'streaming'],
    maxCost: 0.05,
    preferredProviders: ['anthropic', 'openai', 'google']
  }
});
// Automatically selects best provider matching all constraints
```

#### Custom Provider

```typescript
import { BaseLLMProvider, registerProvider } from '@claude-flow/providers';

class CustomProvider extends BaseLLMProvider {
  constructor() {
    super({
      name: 'custom',
      baseURL: 'https://api.example.com/v1',
      defaultModel: 'custom-model',
      capabilities: {
        supportsStreaming: true,
        supportsFunctionCalling: false,
        supportsVision: false
      },
      rateLimits: { requestsPerMinute: 100, tokensPerMinute: 100000 }
    });
  }

  async chat(request) { /* Implementation */ }
  async streamChat(request) { /* Implementation */ }
}

registerProvider(new CustomProvider());
```

#### Multi-Provider Execution

```typescript
import { executeWithMultiple, compareProviders } from '@claude-flow/providers';

// Execute same prompt across multiple providers
const results = await executeWithMultiple(
  ['anthropic', 'openai', 'google'],
  { messages: [{ role: 'user', content: 'Summarize this...' }] }
);

// Compare responses
const comparison = await compareProviders(
  ['gpt-4o', 'claude-opus-4-1'],
  { messages: [{ role: 'user', content: 'Write a function...' }] }
);
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Installation

```bash
npm install @claude-flow/providers
```

### Environment Variables

```bash
ANTHROPIC_API_KEY=sk-ant-...        # Anthropic (Claude)
OPENAI_API_KEY=sk-...               # OpenAI (GPT-4, etc.)
GOOGLE_API_KEY=...                  # Google (Gemini)
AZURE_OPENAI_API_KEY=...            # Azure OpenAI
AZURE_OPENAI_ENDPOINT=...           # Azure endpoint
AWS_ACCESS_KEY_ID=...               # AWS Bedrock
AWS_SECRET_ACCESS_KEY=...           # AWS Bedrock
GROQ_API_KEY=...                    # Groq
TOGETHER_API_KEY=...                # Together AI
FIREWORKS_API_KEY=...               # Fireworks
DEEPSEEK_API_KEY=...                # DeepSeek
MISTRAL_API_KEY=...                 # Mistral
COHERE_API_KEY=...                  # Cohere
REPLICATE_API_TOKEN=...             # Replicate
OPENROUTER_API_KEY=...              # OpenRouter
XAI_API_KEY=...                     # xAI (Grok)
```

### Related Packages

- [@claude-flow/plugins](../plugins) - Provider integration via plugin SDK
- [@claude-flow/integration](../integration) - Deep agentic-flow integration
- [@claude-flow/security](../security) - API key validation

### License

MIT

---

## 🚀 快速开始

```typescript
import { chat, streamChat, embed, getDefaultProviderRegistry } from '@claude-flow/providers';

// Simple chat
const response = await chat({
  provider: 'anthropic',
  model: 'claude-sonnet-4-6',
  messages: [{ role: 'user', content: 'Hello!' }]
});

console.log(response.content);

// Streaming
const stream = await streamChat({
  provider: 'anthropic',
  model: 'claude-opus-4-1',
  messages: [{ role: 'user', content: 'Write a haiku' }],
  stream: true
});

for await (const chunk of stream) {
  process.stdout.write(chunk.content);
}
```

---

## ⚙️ 技术栈

- TypeScript
- Node.js
- 15+ LLM Provider APIs
- SSE streaming