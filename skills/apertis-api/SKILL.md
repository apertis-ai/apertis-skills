---
name: apertis-api
description: Use Apertis API to access 500+ AI models with OpenAI-compatible SDK. Covers authentication, endpoints, model list, web search (:web suffix), and MCP server setup.
version: 1.0.0
author: Apertis
homepage: https://apertis.ai
---

# Apertis API

Apertis is an OpenAI-compatible API gateway providing access to 500+ AI models from 30+ providers (Anthropic, OpenAI, Google, Meta, Mistral, and more).

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

- Get your API key at **https://apertis.ai**
- Header: `Authorization: Bearer YOUR_APERTIS_KEY`
- Keys start with `sk-` (PAYG) or `sk-sub_` (subscription plans)

## API Endpoints

All OpenAI-compatible endpoints are supported:

| Endpoint | Description |
|----------|-------------|
| `POST /v1/chat/completions` | Chat completions (main endpoint) |
| `POST /v1/messages` | Native Anthropic Messages API format |
| `POST /v1/completions` | Legacy text completions |
| `POST /v1/embeddings` | Text embeddings |
| `POST /v1/images/generations` | Image generation |
| `POST /v1/audio/speech` | Text-to-speech |
| `POST /v1/audio/transcriptions` | Speech-to-text (Whisper) |
| `GET /v1/models` | List available models |

## Popular Models

| Model ID | Best For | Cost |
|----------|----------|------|
| `claude-sonnet-4-6` | Coding, complex reasoning | Medium |
| `claude-haiku-4-5` | Fast responses, simple tasks | Low |
| `claude-opus-4-6` | Most capable Claude, long tasks | High |
| `gpt-4o` | Multimodal, vision tasks | Medium |
| `gpt-4o-mini` | Balanced speed and cost | Low |
| `gemini-2.0-flash` | Long context (1M tokens), cheapest | Lowest |
| `gemini-2.5-pro` | Google's most capable | High |
| `deepseek-chat` | Code-focused, cost-efficient | Low |

Use `GET /v1/models` for the full list of 500+ available models.

## Web Search — `:web` Suffix

Add `:web` to **any model ID** to enable real-time web search:

```python
response = client.chat.completions.create(
    model="gpt-4o-mini:web",   # web search enabled
    messages=[{"role": "user", "content": "What happened in AI news today?"}]
)

# Response includes web_sources array
sources = response.choices[0].message.web_sources
# [{"title": "...", "url": "...", "snippet": "..."}]
```

```typescript
const response = await openai.chat.completions.create({
  model: "claude-sonnet-4-6:web",
  messages: [{ role: "user", content: "Latest LLM benchmarks?" }],
});

const sources = (response.choices[0].message as any).web_sources;
```

**Note**: `:free` models cannot use `:web` suffix.

## Example: Chat Completion

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://api.apertis.ai/v1",
    api_key="YOUR_APERTIS_KEY"
)

response = client.chat.completions.create(
    model="claude-sonnet-4-6",
    messages=[
        {"role": "system", "content": "You are a helpful coding assistant."},
        {"role": "user", "content": "Write a Python function to reverse a linked list."}
    ],
    temperature=0.7
)

print(response.choices[0].message.content)
```

## Example: Streaming

```python
stream = client.chat.completions.create(
    model="gpt-4o-mini",
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

vector = response.data[0].embedding  # 1536-dimensional float[]
```

## MCP Server Setup

Use Apertis directly from Claude Code, Cursor, or any MCP-compatible client:

```json
{
  "mcpServers": {
    "apertis": {
      "command": "npx",
      "args": ["-y", "@apertis/mcp"],
      "env": {
        "APERTIS_API_KEY": "YOUR_APERTIS_KEY"
      }
    }
  }
}
```

For Claude Code (`~/.claude.json`), Cursor (`.cursor/mcp.json`), or Claude Desktop (`~/Library/Application Support/Claude/claude_desktop_config.json`).

## Environment Variables

```bash
APERTIS_API_KEY=sk-...        # Your Apertis API key
# Or for subscription plans:
APERTIS_API_KEY=sk-sub_...    # Subscription token
```

## Subscription Plans

| Plan | Price | Best For |
|------|-------|----------|
| Lite | $12/mo | Basic coding, hobby projects |
| Pro | $25/mo | Daily development |
| Plus | $60/mo | Heavy usage |
| Max | $200/mo | Unlimited / teams |

All plans include quota-based access to the full model catalog.
PAYG (pay-as-you-go) is also available with no monthly commitment.

## Error Handling

```python
from openai import OpenAI, APIError, RateLimitError

client = OpenAI(base_url="https://api.apertis.ai/v1", api_key="...")

try:
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": "Hello"}]
    )
except RateLimitError:
    print("Rate limit hit — wait and retry")
except APIError as e:
    print(f"API error {e.status_code}: {e.message}")
```

## Resources

- API Dashboard: https://apertis.ai
- Model Pricing: https://apertis.ai/pricing
- MCP Package: https://www.npmjs.com/package/@apertis/mcp
- Support: hi@apertis.ai
