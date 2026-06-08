---
name: apertis-quickstart
description: Get from zero to your first verified Apertis API call. Guides obtaining a key, setting base_url, making a first chat completion, and confirming web search (:web) works — with step-by-step verification and troubleshooting for common first-call failures (401, wrong base_url, empty web_sources). Use when starting with Apertis, setting it up, making the first call, or when the first call is not working.
version: 1.4.0
author: Apertis
homepage: https://apertis.ai?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem
---

# Apertis Quickstart

Get from zero to your **first verified Apertis call** — including web search — in about two minutes. This is a guided, verify-as-you-go runbook: each step has an expected result and a fix branch, so you confirm it actually works instead of guessing.

> Already calling Apertis and just need reference docs or model picks? Use the **apertis-api** skill (full reference) or **apertis-model-picker** (which model for which task). This skill is only for getting the *first* call working.

## When to use this skill

- "How do I get started with Apertis?" / "Set up Apertis."
- "Make my first Apertis API call."
- "My Apertis call returns 401 / isn't working / `web_sources` is empty."

## What this skill does (and its limits)

When run by an AI coding agent, this skill makes **two real API calls using the user's own key** against `https://api.apertis.ai` (the Apertis gateway) to prove the setup works. Both calls are tiny (short prompt, low `max_tokens`) and cost a negligible amount of the user's quota.

**Boundaries the agent must respect:**
- Use **only the user's own API key**. Never substitute another key.
- Verification is **read-only** from the user's side (stateless chat completions). Do not write to any database, repo, or production system.
- **Never echo the full API key** back to the user or into logs — mask it (`sk-…last4`).
- The agent **cannot sign up or fetch a key** for the user. If there is no key, direct them to get one (Step 0).

---

## Step 0 — Have an API key

Check for the key in the environment first:

```bash
echo "${APERTIS_API_KEY:+key present}"   # prints "key present" if set, nothing if not
```

- **If set** → continue to Step 1.
- **If not set** → get a key at **https://apertis.ai?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem** (sign up → **API Keys** → create), then export it:

  ```bash
  export APERTIS_API_KEY="sk-..."   # PAYG keys start with sk- ; subscription keys with sk-sub_
  ```

**Expected:** a key beginning with `sk-` (pay-as-you-go) or `sk-sub_` (subscription plan).
**If the key looks different** → you likely copied a partial value; recreate it in the dashboard.

## Step 1 — First chat completion

The base URL is **`https://api.apertis.ai/v1`** and the API is OpenAI-compatible. Make one minimal call.

**curl** (works anywhere, no install):

```bash
curl -s https://api.apertis.ai/v1/chat/completions \
  -H "Authorization: Bearer ${APERTIS_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-4o-mini",
    "max_tokens": 32,
    "messages": [{"role": "user", "content": "Reply with exactly: Apertis is working"}]
  }'
```

**Python** (`pip install openai`):

```python
import os
from openai import OpenAI

client = OpenAI(base_url="https://api.apertis.ai/v1", api_key=os.environ["APERTIS_API_KEY"])

resp = client.chat.completions.create(
    model="gpt-4o-mini",
    max_tokens=32,
    messages=[{"role": "user", "content": "Reply with exactly: Apertis is working"}],
)
print(resp.choices[0].message.content)
```

**TypeScript** (`npm install openai`):

```typescript
import OpenAI from "openai";

const client = new OpenAI({
  baseURL: "https://api.apertis.ai/v1",
  apiKey: process.env.APERTIS_API_KEY,
});

const resp = await client.chat.completions.create({
  model: "gpt-4o-mini",
  max_tokens: 32,
  messages: [{ role: "user", content: "Reply with exactly: Apertis is working" }],
});
console.log(resp.choices[0].message.content);
```

**Expected:** HTTP `200` and a non-empty `choices[0].message.content` (the model's reply).
**If it fails** → see [Troubleshooting](#troubleshooting) — most first-call failures are a `401` (key) or a wrong base URL.

## Step 2 — Confirm web search (`:web`)

Add `:web` to **any model id** to enable real-time web search. The response carries a **top-level** `web_sources` array (on the response body, *not* inside `choices[].message`). Ask something only answerable with fresh information so search actually triggers.

**curl** (with a presence check on `web_sources`):

```bash
curl -s https://api.apertis.ai/v1/chat/completions \
  -H "Authorization: Bearer ${APERTIS_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-4o-mini:web",
    "max_tokens": 256,
    "messages": [{"role": "user", "content": "What is one notable AI news item from this week? Cite a source."}]
  }' | python3 -c 'import sys,json; b=json.load(sys.stdin); s=b.get("web_sources") or []; print(f"web_sources: {len(s)} source(s)"); [print(" -", x.get("title"), x.get("url")) for x in s[:3]]'
```

**Python:**

```python
resp = client.chat.completions.create(
    model="gpt-4o-mini:web",
    max_tokens=256,
    messages=[{"role": "user", "content": "What is one notable AI news item from this week? Cite a source."}],
)
sources = getattr(resp, "web_sources", None) or []   # top-level on the response
print(f"web_sources: {len(sources)} source(s)")
for s in sources[:3]:
    print(" -", s["title"], s["url"])
```

**TypeScript:**

```typescript
const resp = await client.chat.completions.create({
  model: "gpt-4o-mini:web",
  max_tokens: 256,
  messages: [{ role: "user", content: "What is one notable AI news item from this week? Cite a source." }],
});
const sources = (resp as any).web_sources ?? [];   // top-level on the response
console.log(`web_sources: ${sources.length} source(s)`);
sources.slice(0, 3).forEach((s: any) => console.log(" -", s.title, s.url));
```

**Expected:** a non-empty `web_sources` array, each item with `title`, `url`, and `snippet`.
**If `web_sources` is empty or missing** → see [Troubleshooting](#troubleshooting). Note: `:free` models cannot use `:web`.

## Step 3 — You're set up ✅

If Steps 1 and 2 both passed, Apertis is working end to end. To make the base URL permanent, set it once in your client config or keep `APERTIS_API_KEY` in your environment.

**Next:**
- **apertis-model-picker** — pick the right model for coding, long context, reasoning, web search, or cost.
- **apertis-api** — full endpoint reference (embeddings, images, audio, Vercel AI SDK provider, MCP server).
- Current model list & pricing: https://apertis.ai/pricing?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| `401` / "Invalid API Key" | Key missing, truncated, or wrong env var | Recheck `APERTIS_API_KEY`; recreate the key at https://apertis.ai?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem → API Keys. Confirm the header is `Authorization: Bearer <key>`. |
| Connection error / 404 on the URL | Wrong base URL | Must be exactly `https://api.apertis.ai/v1` (note `api.` and the `/v1` suffix). |
| `model_not_found` / unknown model | Model id changed or not enabled | Run `curl -s https://api.apertis.ai/v1/models -H "Authorization: Bearer ${APERTIS_API_KEY}"` to list available ids, or check https://apertis.ai/pricing?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem, then swap the `model` value. |
| `web_sources` empty or missing | `:web` suffix dropped, a `:free` model used, or the prompt didn't need fresh info | Confirm the model id ends in `:web`; use a non-`:free` model; ask a question that requires current information. |
| `429` / rate limited | Per-key request rate exceeded | Wait briefly and retry; heavy usage may need a higher plan (https://apertis.ai/pricing?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem). |
| Insufficient quota / payment error | Account has no remaining quota | Top up or choose a plan at https://apertis.ai?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem. |

## Resources

- Get a key & dashboard: https://apertis.ai?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem
- Model list & pricing: https://apertis.ai/pricing?utm_source=apertis-skills&utm_medium=skill-doc&utm_campaign=ecosystem
- Support: hi@apertis.ai
