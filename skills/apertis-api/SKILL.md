---
name: apertis-api
description: Use Apertis API to access 500+ AI models with OpenAI-compatible SDK. Covers authentication, endpoints, popular model families, web search (:web suffix), the Vercel AI SDK provider, and MCP server setup.
version: 1.4.0
author: Apertis
homepage: https://apertis.ai?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem
---

# Apertis API

Apertis is an OpenAI-compatible API gateway providing access to 500+ AI models from 30+ providers (Anthropic, OpenAI, Google, DeepSeek, Mistral, MiniMax, GLM, and more).

> **Model IDs change frequently.** Always check https://apertis.ai/pricing?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem for the current model list and pricing.

## Quick Start — One Line to Switch

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://api.apertis.ai/v1",
    api_key="YOUR_APERTIS_KEY"
)
```

```typescript
import OpenAI from "openai";

const openai = new OpenAI({
  baseURL: "https://api.apertis.ai/v1",
  apiKey: process.env.APERTIS_API_KEY,
});
```

## Authentication

- Get your API key at **https://apertis.ai?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem**
- Header: `Authorization: Bearer YOUR_APERTIS_KEY`
- Keys start with `sk-` (PAYG) or `sk-sub_` (subscription plans)

## API Endpoints

All OpenAI-compatible endpoints are supported:

| Endpoint | Description |
|----------|-------------|
| `POST /v1/chat/completions` | Chat completions (main endpoint) |
| `POST /v1/messages` | Native Anthropic Messages API format |
| `POST /v1/embeddings` | Text embeddings |
| `POST /v1/images/generations` | Image generation |
| `POST /v1/audio/speech` | Text-to-speech |
| `POST /v1/audio/transcriptions` | Speech-to-text |
| `GET /v1/models` | List all available models |

## Model Families

Apertis carries the latest models from every major provider. Use `GET /v1/models` or visit https://apertis.ai/pricing?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem for the full current list.

| Provider | Family | Notes |
|----------|--------|-------|
| Anthropic | `claude-sonnet-4-*`, `claude-opus-4-*`, `claude-haiku-4-*` | Best for coding |
| OpenAI | `gpt-5-*`, `gpt-4o`, `o4-mini` | GPT and reasoning series |
| Google | `gemini-3-*`, `gemini-2.5-*` | Long context, multimodal |
| DeepSeek | `deepseek-v3`, `deepseek-r1` | Cost-efficient, strong reasoning |
| MiniMax | `minimax-m1` | Long context alternative |
| GLM | `glm-4.5-*` | Multilingual, cost-efficient |
| Meta | `llama-4-*`, `llama-3.3-*` | Open-weight models |
| Mistral | `mistral-medium-*`, `mistral-small-*` | European, privacy-focused |

## Web Search — `:web` Suffix

Add `:web` to **any model ID** to enable real-time web search:

```python
response = client.chat.completions.create(
    model="gpt-4o:web",
    messages=[{"role": "user", "content": "What happened in AI news today?"}]
)

# Response includes a top-level web_sources array (on the response, not inside choices[].message)
sources = response.web_sources
# [{"title": "...", "url": "...", "snippet": "..."}]
```

**Note**: `:free` models cannot use `:web` suffix.

## Example: Chat Completion

```python
response = client.chat.completions.create(
    model="claude-sonnet-4-6",   # check apertis.ai/pricing for latest Claude Sonnet ID
    messages=[
        {"role": "system", "content": "You are a helpful coding assistant."},
        {"role": "user", "content": "Write a Python function to reverse a linked list."}
    ]
)
print(response.choices[0].message.content)
```

## Example: Streaming

```python
stream = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Explain async/await in JavaScript"}],
    stream=True
)
for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="")
```

## Example: Embeddings

```python
response = client.embeddings.create(
    model="text-embedding-3-small",
    input="Your text to embed"
)
vector = response.data[0].embedding
```

## Vercel AI SDK — Native Provider

For TypeScript apps and agents built on the [Vercel AI SDK](https://sdk.vercel.ai/) (v5+), use the native Apertis provider instead of the raw OpenAI SDK. Works with OpenCode, Kilo Code, Cursor, and any AI-SDK-based tool.

```bash
npm install @apertis/ai-sdk-provider ai
```

```typescript
import { apertis } from "@apertis/ai-sdk-provider";
import { generateText } from "ai";

const { text } = await generateText({
  model: apertis("claude-sonnet-4-6"),   // any of 500+ models
  prompt: "Explain quantum computing in simple terms.",
});
```

Streaming, tool calling, and embeddings all work through the standard AI SDK functions (`streamText`, `tool`, `embed`). Set `APERTIS_API_KEY` in the environment, or pass it explicitly:

```typescript
import { createApertis } from "@apertis/ai-sdk-provider";

const apertis = createApertis({ apiKey: process.env.APERTIS_API_KEY });
```

## MCP Server Setup

Use Apertis directly from Claude Code, Cursor, or any MCP-compatible client:

```json
{
  "mcpServers": {
    "apertis": {
      "command": "npx",
      "args": ["-y", "@apertis/mcp-server"],
      "env": {
        "APERTIS_API_KEY": "YOUR_APERTIS_KEY"
      }
    }
  }
}
```

Place in Claude Code (`~/.claude.json`), Cursor (`.cursor/mcp.json`), or Claude Desktop (`~/Library/Application Support/Claude/claude_desktop_config.json`).

## Subscription Plans

| Plan | Price | Best For |
|------|-------|----------|
| Lite | $12/mo | Basic coding, hobby projects |
| Pro | $25/mo | Daily development |
| Plus | $60/mo | Heavy usage |
| Max | $200/mo | Unlimited / teams |

PAYG (pay-as-you-go) is also available with no monthly commitment.

## Resources

- Full model list & pricing: https://apertis.ai/pricing?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem
- MCP Package: https://www.npmjs.com/package/@apertis/mcp-server
- Support: hi@apertis.ai
