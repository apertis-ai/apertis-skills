# Apertis Agent Skills

Agent skills for [Apertis](https://apertis.ai) — OpenAI-compatible API gateway giving you access to 500+ AI models from 30+ providers.

## Install

```bash
npx skills add theQuert/apertis-skills
```

Works with Claude Code, Cursor, GitHub Copilot, Codex, Gemini CLI, and [45+ other AI coding tools](https://agentskills.io).

## Skills

| Skill | Description |
|-------|-------------|
| [`apertis-api`](skills/apertis-api/SKILL.md) | Complete Apertis API reference — auth, models, `:web` search, MCP server |
| [`apertis-model-picker`](skills/apertis-model-picker/SKILL.md) | Opinionated model recommendations by task type |
| [`apertis-migrate`](skills/apertis-migrate/SKILL.md) | Migrate from OpenAI SDK to Apertis in one line |

## What You Get After Install

Your AI coding assistant will know:

1. How to authenticate and call the Apertis API (OpenAI-compatible)
2. Which model to use for each task — with reasoning
3. How to enable web search on any model (`:web` suffix)
4. How to set up the Apertis MCP server
5. How to migrate from OpenAI with a one-line change

## Quick Example

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://api.apertis.ai/v1",
    api_key="YOUR_APERTIS_KEY"  # from apertis.ai
)

response = client.chat.completions.create(
    model="claude-sonnet-4-6",   # or any 500+ models
    messages=[{"role": "user", "content": "Hello!"}]
)
```

Add `:web` to any model for real-time web search:

```python
model="gpt-4o-mini:web"  # response includes web_sources[]
```

## Links

- [Apertis](https://apertis.ai) — Get your API key
- [Model list](https://apertis.ai/pricing) — All 500+ models and pricing
- [MCP Server](https://www.npmjs.com/package/@apertis/mcp) — Use Apertis from any MCP client
- [Agent Skills spec](https://agentskills.io) — How skills work
