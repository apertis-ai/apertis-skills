---
name: apertis-model-picker
description: Opinionated model recommendations for Apertis API by task type — coding, long context, fast chat, web search, image understanding, reasoning, and cost optimization.
version: 1.3.0
author: Apertis
homepage: https://apertis.ai
---

# Apertis Model Picker

Opinionated recommendations by task type. Model IDs evolve quickly — always verify current IDs and pricing at https://apertis.ai/pricing.

## Coding & Debugging

**Recommended: Claude Sonnet** (`claude-sonnet-4-6` or latest `claude-sonnet-4-*`)
- Best coding ability per dollar on the platform
- Excels at multi-file edits, debugging, code review, architecture planning
- Context: 200K tokens

```python
client.chat.completions.create(
    model="claude-sonnet-4-6",
    messages=[{"role": "user", "content": "Refactor this function for better performance..."}]
)
```

**Faster/cheaper**: Claude Haiku (`claude-haiku-4-5` or latest `claude-haiku-4-*`)
**Most capable**: Claude Opus (`claude-opus-4-*`)

---

## Long Context — Large Files, Full Codebases

**Recommended: Gemini Flash** (`gemini-3-flash-preview` or latest `gemini-3-*`)
- 1M+ token context window (fits entire codebases)
- Fast and cost-efficient
- Ideal for: analyzing large repos, summarizing long docs, multi-file reasoning

```python
client.chat.completions.create(
    model="gemini-3-flash-preview",
    messages=[{"role": "user", "content": "Analyze this entire codebase and identify architectural issues..."}]
)
```

**Higher quality**: Gemini Pro (`gemini-3-pro` or `gemini-2.5-pro`)

---

## Fast Chat — Customer Support, Simple Q&A

**Recommended: GPT-4o or GPT-5 mini**
- Fast, cost-efficient, reliable structured output
- Good at following system prompt instructions precisely

```python
client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": "You are a helpful customer support agent."},
        {"role": "user", "content": "How do I reset my password?"}
    ]
)
```

**Budget alternative**: `glm-4.5-flash` or `minimax-m1` — strong multilingual, very low cost

---

## Web Search — Real-Time Information

**Recommended: any model + `:web` suffix**
- Works with any non-free model on Apertis
- Returns `web_sources[]` with title, URL, snippet

```python
response = client.chat.completions.create(
    model="gpt-4o:web",
    messages=[{"role": "user", "content": "What's the latest Claude model?"}]
)
sources = response.choices[0].message.web_sources
```

Start cheap (`gpt-4o:web`), upgrade to Claude Sonnet or Gemini Pro if you need deeper synthesis.

---

## Reasoning — Math, Logic, Complex Problem Solving

**Recommended: `deepseek-r1`**
- Chain-of-thought reasoning, shows its work
- Strong math and logic performance
- Cost-efficient vs OpenAI o-series

```python
client.chat.completions.create(
    model="deepseek-r1",
    messages=[{"role": "user", "content": "Solve this step by step..."}]
)
```

**OpenAI option**: `o4-mini` (reliable, fast reasoning)

---

## Image Understanding & Vision

**Recommended: `gpt-4o`**
- Strong multimodal understanding
- Handles: screenshots, diagrams, charts, handwriting, UI mockups

```python
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{
        "role": "user",
        "content": [
            {"type": "text", "text": "What's in this image?"},
            {"type": "image_url", "image_url": {"url": "data:image/jpeg;base64,..."}}
        ]
    }]
)
```

**Also strong**: Gemini Flash/Pro (vision + very long context)

---

## Cost Optimization — Budget Projects, High Volume

**Recommended: Gemini Flash or GLM**
- `gemini-3-flash-preview` — very low cost, large context
- `glm-4.5-air` — multilingual, competitive pricing
- `deepseek-v3` — excellent for coding-heavy workloads

---

## Quick Reference

| Task | Model Family | Notes |
|------|-------------|-------|
| Coding / debugging | Claude Sonnet | Best coding per dollar |
| Long context (1M+) | Gemini Flash | Fast, cheap, huge context |
| Fast chat / support | GPT-4o or GLM | Reliable, cost-efficient |
| Web search | Any model + `:web` | All non-free models supported |
| Reasoning / math | DeepSeek R1 | Shows reasoning steps |
| Vision / images | GPT-4o or Gemini | Strong multimodal |
| Budget / high volume | Gemini Flash / GLM | Lowest cost tier |
| Most capable overall | Claude Opus | When quality > cost |

Check live model IDs and pricing at https://apertis.ai/pricing

---

## Dynamic Model Selection (Recommended for Agents)

Instead of relying on this static list, call the Apertis Recommend API to get the best model for your task at the current time, with live pricing:

```bash
curl "https://api.apertis.ai/v1/recommend?task=coding&budget=medium" \
  -H "Authorization: Bearer $APERTIS_API_KEY"
```

**Task types**: `coding`, `long-context`, `fast-chat`, `reasoning`, `vision`
**Budget tiers**: `low` (cheapest), `medium` (balanced), `high` (best quality)

**Example response**:
```json
{
  "model": "claude-sonnet-4-6",
  "task": "coding",
  "budget": "medium",
  "input_price_per_1m": 2.40,
  "output_price_per_1m": 12.00,
  "reason": "Best coding ability per dollar. 200K context.",
  "alternatives": [
    { "model": "deepseek-v3", "input_price_per_1m": 0.30, "note": "3x cheaper, good for simpler coding tasks" }
  ]
}
```

Use the returned `model` value directly in your API calls. This endpoint always reflects current pricing and model availability.
