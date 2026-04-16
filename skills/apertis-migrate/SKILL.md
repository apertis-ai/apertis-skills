---
name: apertis-migrate
description: Migrate from OpenAI SDK to Apertis API in one line. Works with Python, TypeScript/JavaScript, curl, and LangChain. Same SDK, just change the base URL.
version: 1.0.0
author: Apertis
homepage: https://apertis.ai
---

# Migrate from OpenAI to Apertis

Apertis is 100% OpenAI-compatible. Migration is one line — change `base_url` / `baseURL` and your API key. No other code changes needed.

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

Update your `.env` file — your code reads `process.env.APERTIS_API_KEY` (or set it as `OPENAI_API_KEY` if you don't want to change env var names).

## curl

```bash
# Before
curl https://api.openai.com/v1/chat/completions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{"model": "gpt-4o-mini", "messages": [...]}'

# After — only the URL changes
curl https://api.apertis.ai/v1/chat/completions \
  -H "Authorization: Bearer $APERTIS_API_KEY" \
  -d '{"model": "gpt-4o-mini", "messages": [...]}'
```

## LangChain (Python)

```python
# Before
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o-mini")

# After
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(
    model="gpt-4o-mini",
    openai_api_base="https://api.apertis.ai/v1",
    openai_api_key="YOUR_APERTIS_KEY"
)
```

## LangChain (TypeScript)

```typescript
import { ChatOpenAI } from "@langchain/openai";

const llm = new ChatOpenAI({
  modelName: "gpt-4o-mini",
  configuration: {
    baseURL: "https://api.apertis.ai/v1",
    apiKey: process.env.APERTIS_API_KEY,
  },
});
```

## Model Names After Migration

You can keep your existing OpenAI model names — they still work:

```python
model="gpt-4o"       # still works
model="gpt-4o-mini"  # still works
model="gpt-4-turbo"  # still works
```

Or switch to any of 500+ models:

```python
model="claude-sonnet-4-6"   # often cheaper, better coding
model="gemini-2.0-flash"    # cheapest, 1M context
model="deepseek-chat"       # budget coding option
```

## Codebase Migration Script

Let your AI assistant auto-migrate your codebase:

1. Find all OpenAI client initializations:
   - `new OpenAI(` (TypeScript)
   - `openai.OpenAI(` (Python)
   - `OpenAI(api_key=` (Python)

2. Add `base_url="https://api.apertis.ai/v1"` (Python) or `baseURL: "https://api.apertis.ai/v1"` (TypeScript)

3. Replace `OPENAI_API_KEY` → `APERTIS_API_KEY` in env files and config

4. Update `.env.example` with the new variable name

## Estimated Cost Savings

Apertis subscription plans offer predictable monthly costs:
- Lite $12/mo, Pro $25/mo, Plus $60/mo, Max $200/mo

For high-volume PAYG usage, Apertis routes to the most cost-efficient provider per model.
Check https://apertis.ai/pricing for current model pricing.

## What You Can Do After Migration

Add `:web` to any model for real-time web search:

```python
model="gpt-4o-mini:web"   # same model + live web results
# response.choices[0].message.web_sources → [{title, url, snippet}]
```

Access 500+ models without switching SDKs:

```python
model="claude-sonnet-4-6"   # Anthropic
model="gemini-2.0-flash"    # Google
model="llama-3.3-70b"       # Meta (via Groq/Together)
```

## Get Your API Key

1. Go to https://apertis.ai
2. Sign up or log in
3. Navigate to API Keys
4. Create a new key

Your key starts with `sk-` (PAYG) or `sk-sub_` (subscription).
