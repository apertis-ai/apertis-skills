---
name: apertis-model-picker
description: Opinionated model recommendations for Apertis API by task type — coding, long context, fast chat, web search, image understanding, and cost optimization.
version: 1.0.0
author: Apertis
homepage: https://apertis.ai
---

# Apertis Model Picker

Opinionated model recommendations based on real usage patterns across Apertis users.
Stop guessing which model to use — here's what actually works best for each task.

## Coding & Debugging

**Recommended: `claude-sonnet-4-6`**
- Best coding ability per dollar on the platform
- Excels at multi-file edits, debugging, code review, architecture planning
- Context: 200K tokens

```python
client.chat.completions.create(
    model="claude-sonnet-4-6",
    messages=[{"role": "user", "content": "Refactor this function for better performance..."}]
)
```

**Faster alternative**: `claude-haiku-4-5` (3-5x cheaper, great for autocomplete)
**Most capable**: `claude-opus-4-6` (use when sonnet isn't enough)

---

## Long Context — Large Files, Long Documents

**Recommended: `gemini-2.0-flash`**
- 1M token context window (fits entire codebases)
- Cheapest model per token on the platform
- Ideal for: summarizing large PDFs, analyzing full repos, long conversation history

```python
client.chat.completions.create(
    model="gemini-2.0-flash",
    messages=[{"role": "user", "content": "Analyze this 500-page document..."}]
)
```

**Stronger reasoning**: `gemini-2.5-pro` (larger context + better quality, higher cost)

---

## Fast Chat — Customer Support, Simple Q&A

**Recommended: `gpt-4o-mini`**
- Fast, stable, cost-efficient
- Consistent response format (great for structured outputs)
- Good at following system prompt instructions precisely

```python
client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "system", "content": "You are a helpful customer support agent."},
        {"role": "user", "content": "How do I reset my password?"}
    ]
)
```

---

## Web Search — Real-Time Information

**Recommended: `gpt-4o-mini:web`**
- Cheapest web-enabled model combination
- Returns `web_sources[]` with title, URL, snippet
- Great for: news, current prices, recent docs, live data

```python
response = client.chat.completions.create(
    model="gpt-4o-mini:web",
    messages=[{"role": "user", "content": "What's the latest Claude model?"}]
)
sources = response.choices[0].message.web_sources
```

**Deeper analysis**: `claude-sonnet-4-6:web` (better synthesis, higher cost)

Any model supports `:web` — just append it to the model ID.

---

## Image Understanding & Vision

**Recommended: `gpt-4o`**
- Best multimodal understanding on the platform
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

---

## Cost Optimization — Budget Projects, High Volume

**Recommended: `gemini-2.0-flash`**
- Among the lowest per-token pricing on the platform
- Surprisingly capable for its price tier
- Best for: high-volume pipelines, hobby projects, prototyping

**Also consider**: `deepseek-chat` for coding tasks with tight budgets

---

## Structured Output / JSON Mode

**Recommended: `gpt-4o-mini`**
- Most reliable at following JSON schemas
- Consistently produces valid JSON even with complex schemas

```python
response = client.chat.completions.create(
    model="gpt-4o-mini",
    response_format={"type": "json_object"},
    messages=[{"role": "user", "content": "Extract the name, email, and company from: John Smith, john@acme.com, Acme Corp"}]
)
```

---

## Quick Reference

| Task | Model | Notes |
|------|-------|-------|
| Coding / debugging | `claude-sonnet-4-6` | Best coding per dollar |
| Long documents (1M+) | `gemini-2.0-flash` | Cheapest, largest context |
| Fast chat / support | `gpt-4o-mini` | Stable, cost-efficient |
| Web search | `gpt-4o-mini:web` | Any model + `:web` works |
| Vision / images | `gpt-4o` | Best multimodal |
| Budget / high volume | `gemini-2.0-flash` | Lowest cost |
| Structured JSON | `gpt-4o-mini` | Most reliable format |
| Most capable overall | `claude-opus-4-6` | When quality > cost |

## How to Choose

1. **Default for code**: `claude-sonnet-4-6`
2. **Need web info**: add `:web` to any model
3. **Need to be cheap**: `gemini-2.0-flash`
4. **Need images**: `gpt-4o`
5. **Not sure**: `gpt-4o-mini` is a solid all-rounder

Check live pricing at https://apertis.ai/pricing
