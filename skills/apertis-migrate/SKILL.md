---
name: apertis-migrate
description: Migrate from OpenAI SDK to Apertis API in one line. Works with Python, TypeScript/JavaScript, curl, LangChain, and the Vercel AI SDK. Same SDK, just change the base URL.
version: 1.4.0
author: Apertis
homepage: https://apertis.ai?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem
---

# Migrate from OpenAI to Apertis

Apertis is 100% OpenAI-compatible. Migration is one line — change `base_url` / `baseURL` and your API key. No other code changes needed.

> **Model IDs change frequently.** You can keep your existing OpenAI model names (they still work), or check https://apertis.ai/pricing?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem for the full current list across 500+ models.

## Python

```python
# Before
from openai import OpenAI
client = OpenAI(api_key="sk-...")

# After — only base_url changes
from openai import OpenAI
client = OpenAI(
    base_url="https://api.apertis.ai/v1",
    api_key="YOUR_APERTIS_KEY"
)
```

Everything else stays identical: `client.chat.completions.create()`, `client.embeddings.create()`, streaming, async — all unchanged.

## TypeScript / JavaScript

```typescript
// Before
import OpenAI from "openai";
const openai = new OpenAI({ apiKey: "sk-..." });

// After — only baseURL changes
import OpenAI from "openai";
const openai = new OpenAI({
  baseURL: "https://api.apertis.ai/v1",
  apiKey: process.env.APERTIS_API_KEY,
});
```

## Environment Variables

```bash
# Remove
OPENAI_API_KEY=sk-...

# Add
APERTIS_API_KEY=sk-...   # your Apertis key from apertis.ai
```

## curl

```bash
# Before
curl https://api.openai.com/v1/chat/completions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{"model": "gpt-4o", "messages": [...]}'

# After — only the URL changes
curl https://api.apertis.ai/v1/chat/completions \
  -H "Authorization: Bearer $APERTIS_API_KEY" \
  -d '{"model": "gpt-4o", "messages": [...]}'
```

## LangChain (Python)

```python
# Before
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o")

# After
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(
    model="gpt-4o",
    openai_api_base="https://api.apertis.ai/v1",
    openai_api_key="YOUR_APERTIS_KEY"
)
```

## LangChain (TypeScript)

```typescript
import { ChatOpenAI } from "@langchain/openai";

const llm = new ChatOpenAI({
  modelName: "gpt-4o",
  configuration: {
    baseURL: "https://api.apertis.ai/v1",
    apiKey: process.env.APERTIS_API_KEY,
  },
});
```

## Vercel AI SDK

If your app or agent is built on the [Vercel AI SDK](https://sdk.vercel.ai/) (OpenCode, Kilo Code, Cursor, etc.), use the native Apertis provider instead of the OpenAI-compatible base URL:

```bash
npm install @apertis/ai-sdk-provider ai
```

```typescript
// Before
import { openai } from "@ai-sdk/openai";
import { generateText } from "ai";
const { text } = await generateText({ model: openai("gpt-4o"), prompt: "..." });

// After — swap the provider import
import { apertis } from "@apertis/ai-sdk-provider";
import { generateText } from "ai";
const { text } = await generateText({ model: apertis("gpt-4o"), prompt: "..." });
```

Set `APERTIS_API_KEY` in your environment. Everything else (`streamText`, `tool`, `embed`) stays identical.

## Model Names After Migration

You can keep your existing OpenAI model names — they still work:

```python
model="gpt-4o"        # still works
model="gpt-4o-mini"   # still works
model="gpt-4-turbo"   # still works
```

Or switch to any of 500+ models across all providers:

```python
model="claude-sonnet-4-6"      # Anthropic — best coding per dollar
model="gemini-3-flash-preview"  # Google — cheap, 1M token context
model="deepseek-r1"             # DeepSeek — reasoning tasks
model="deepseek-v3"             # DeepSeek — budget coding option
```

Check the full current model list and pricing at https://apertis.ai/pricing?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem

## Codebase Migration — Let Your AI Assistant Do It

Ask your AI coding assistant to:

1. Find all OpenAI client initializations:
   - `new OpenAI(` (TypeScript/JS)
   - `OpenAI(api_key=` or `openai.OpenAI(` (Python)

2. Add `base_url="https://api.apertis.ai/v1"` (Python) or `baseURL: "https://api.apertis.ai/v1"` (TypeScript)

3. Replace `OPENAI_API_KEY` → `APERTIS_API_KEY` in `.env` files

4. Update `.env.example` with the new variable

## What You Unlock After Migration

Add `:web` to any model for real-time web search:

```python
model="gpt-4o:web"
# response includes: web_sources → [{title, url, snippet}]
```

Access 500+ models without switching SDKs:

```python
model="claude-sonnet-4-6"      # Anthropic
model="gemini-3-flash-preview"  # Google
model="deepseek-r1"             # DeepSeek — reasoning
model="minimax-m1"              # MiniMax — long context alternative
```

## Get Your API Key

1. Go to https://apertis.ai?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem
2. Sign up or log in
3. Navigate to API Keys
4. Create a new key

Your key starts with `sk-` (PAYG) or `sk-sub_` (subscription).
