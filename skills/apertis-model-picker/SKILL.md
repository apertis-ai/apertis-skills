---
name: apertis-model-picker
description: Opinionated model recommendations for Apertis API by task type — coding, long context, fast chat, web search, image understanding, reasoning, and cost optimization.
version: 1.1.0
author: Apertis
homepage: https://apertis.ai
---

# Apertis Model Picker

Opinionated model recommendations based on real usage patterns across Apertis users.

## Coding & Debugging

**Recommended: `claude-sonnet-4-20250514`**
- Best coding ability per dollar on the platform
- Excels at multi-file edits, debugging, code review, architecture planning
- Context: 200K tokens

```python
client.chat.completions.create(
    model="claude-sonnet-4-20250514",
    messages=[{"role": "user", "content": "Refactor this function for better performance..."}]
)
```

**Faster/cheaper**: `claude-3-5-haiku-20241022` (great for autocomplete, quick edits)
**Most capable**: `claude-opus-4-1-20250805` (use when sonnet isn't enough)

---

## Long Context — Large Files, Full Codebases

**Recommended: `gemini-2.5-flash`**
- 1M token context window (fits entire codebases)
- Fast and cost-efficient
- Ideal for: analyzing large repos, summarizing long docs, multi-file reasoning

```python
client.chat.completions.create(
    model="gemini-2.5-flash",
    messages=[{"role": "user", "content": "Analyze this entire codebase and identify architectural issues..."}]
)
```

**Higher quality**: `gemini-2.5-pro` (better reasoning, same large context, higher cost)

---

## Fast Chat — Customer Support, Simple Q&A

**Recommended: `gpt-4.1-mini`**
- Fast, cost-efficient, reliable
- Consistent response format (great for structured outputs)
- Good at following system prompt instructions precisely

```python
client.chat.completions.create(
    model="gpt-4.1-mini",
    messages=[
        {"role": "system", "content": "You are a helpful customer support agent."},
        {"role": "user", "content": "How do I reset my password?"}
    ]
)
```

---

## Web Search — Real-Time Information

**Recommended: `gpt-4.1-mini:web`**
- Cheapest web-enabled model combination
- Returns `web_sources[]` with title, URL, snippet
- Great for: news, current pricing, recent docs, live data

```python
response = client.chat.completions.create(
    model="gpt-4.1-mini:web",
    messages=[{"role": "user", "content": "What's the latest Claude model?"}]
)
sources = response.choices[0].message.web_sources
```

**Deeper analysis**: `claude-sonnet-4-20250514:web` (better synthesis, higher cost)

Any model supports `:web` — just append it to the model ID.

---

## Reasoning — Math, Logic, Complex Problem Solving

**Recommended: `deepseek-r1`**
- Chain-of-thought reasoning, shows its work
- Strong math and logic performance
- Cost-efficient vs OpenAI o-series

```python
client.chat.completions.create(
    model="deepseek-r1",
    messages=[{"role": "user", "content": "Prove that sqrt(2) is irrational."}]
)
```

**OpenAI option**: `o4-mini` (fast reasoning, reliable)
**Most capable reasoning**: `o4-mini-high` or `claude-opus-4-1-20250805`

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

**Also good**: `gemini-2.5-flash` (vision + long context)

---

## Cost Optimization — Budget Projects, High Volume

**Recommended: `gemini-2.5-flash`**
- Very low per-token cost
- Solid quality for most tasks
- Best for: high-volume pipelines, prototyping, hobby projects

**Also consider**: `deepseek-v3` for coding-heavy workloads, `gpt-4.1-nano` for simple tasks

---

## Structured Output / JSON Mode

**Recommended: `gpt-4.1-mini`**
- Most reliable at following JSON schemas
- Consistently produces valid JSON with complex schemas

```python
response = client.chat.completions.create(
    model="gpt-4.1-mini",
    response_format={"type": "json_object"},
    messages=[{"role": "user", "content": "Extract name, email, company from: John Smith, john@acme.com, Acme Corp"}]
)
```

---

## Quick Reference

| Task | Model | Notes |
|------|-------|-------|
| Coding / debugging | `claude-sonnet-4-20250514` | Best coding per dollar |
| Long context (1M+) | `gemini-2.5-flash` | Fast, cheap, huge context |
| Fast chat / support | `gpt-4.1-mini` | Reliable, cost-efficient |
| Web search | `gpt-4.1-mini:web` | Any model + `:web` works |
| Reasoning / math | `deepseek-r1` | Shows reasoning steps |
| Vision / images | `gpt-4o` | Best multimodal |
| Budget / high volume | `gemini-2.5-flash` | Lowest cost tier |
| Structured JSON | `gpt-4.1-mini` | Most reliable format |
| Most capable overall | `claude-opus-4-1-20250805` | When quality > cost |

## How to Choose

1. **Default for code**: `claude-sonnet-4-20250514`
2. **Need web info**: add `:web` to any model
3. **Need to be cheap**: `gemini-2.5-flash`
4. **Need images**: `gpt-4o`
5. **Need reasoning**: `deepseek-r1` or `o4-mini`
6. **Not sure**: `gpt-4.1-mini` is a solid all-rounder

Check live pricing at https://apertis.ai/pricing
