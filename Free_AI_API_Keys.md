# Free AI API Keys

A reference guide to AI providers offering free API access — no credit card required (unless noted). All entries have ongoing free tiers, not just one-time trial credits.

> **Note:** Rate limits, model availability, and free tier terms change frequently. Always verify current limits on the provider's pricing page before building on a free tier. Last reviewed May 2026.

---

## Quick Reference

| Provider | Notable Free Models | OpenAI-Compatible | Credit Card Required |
|---|---|---|---|
| [NVIDIA NIM](#nvidia-nim) | DeepSeek V4 Flash/Pro, Kimi K2.6, Qwen3 Coder, Llama 4, GLM-5.1, 100+ more | Yes | No |
| [Groq](#groq) | GPT-OSS 120B/20B, Llama 3.3 70B, Llama 4 Scout | Yes | No |
| [Google AI Studio](#google-ai-studio) | Gemini 3 Flash, Gemini 3.1 Flash-Lite, Gemini 2.5 Flash | Yes | No |
| [Cerebras](#cerebras) | Llama 3.3 70B, Qwen3 32B/235B, GPT-OSS 120B | Yes | No |
| [SambaNova](#sambanova) | Llama 4 Scout/Maverick, DeepSeek V3.1, QwQ-32B | Yes | No |
| [OpenRouter](#openrouter) | Many (`:free` tagged models) | Yes | No |
| [Hugging Face](#hugging-face) | Thousands of models | Partial | No |
| [Cloudflare Workers AI](#cloudflare-workers-ai) | Llama, Mistral, image-gen, and more | Partial | No (Cloudflare account) |
| [Mistral AI](#mistral-ai) | Mistral Small and others | Yes | No |
| [Cohere](#cohere) | Command, Embed, Rerank | No (own SDK) | No |

---

## NVIDIA NIM

**URL:** https://build.nvidia.com

The largest free catalog around — 100+ frontier hosted models via the NVIDIA Developer Program. As of May 2026 the lineup includes the new DeepSeek V4 family, Kimi K2.6, Qwen3 series, Llama 4, GLM-5.1, MiniMax M2.7, and many Nemotron variants.

- **Sign-up:** Free NVIDIA Developer account, no credit card
- **Notable free models:**
  - `deepseek-ai/deepseek-v4-flash` (284B MoE, 1M context, coding/agentic)
  - `deepseek-ai/deepseek-v4-pro` (1.6T MoE, 1M context)
  - `moonshotai/kimi-k2.6` (256K context, long-horizon agentic)
  - `qwen/qwen3-coder-480b-a35b-instruct` (code specialist)
  - `meta/llama-4-maverick-17b-128e-instruct`, `meta/llama-4-scout-17b-16e-instruct`
  - `zai-org/glm-5.1-air` (agentic workflows)
  - `nvidia/llama-3.3-nemotron-super-49b`
- **OpenAI-compatible:** Yes — `https://integrate.api.nvidia.com/v1`
- **Key prefix:** `nvapi-`
- **Rate limits:** ~40 requests/min on free tier, varies by model
- **Standout feature:** One API key, 100+ models. Largest free hosted catalog available.
- **Guides in this repo:** [Aider + NVIDIA NIM](Aider_Setup_NVIDIA_NIM.md) | [OpenCode + NVIDIA NIM](OpenCode_Setup_NVIDIA_NIM.md)

> **Heads-up on retired models:** Moonshot's Kimi K2.5 and K2 (Instruct/Thinking) were retired from NVIDIA NIM in April–May 2026. K2.6 is the current Kimi offering but has noticeably higher latency than DeepSeek or Qwen alternatives.

---

## Groq

**URL:** https://console.groq.com

Extremely fast inference (custom LPU hardware). One of the fastest free APIs available.

- **Sign-up:** Free account, no credit card
- **Notable free models:**
  - `openai/gpt-oss-120b` (~500 t/s)
  - `openai/gpt-oss-20b` (~1000 t/s — fastest production model)
  - `llama-3.3-70b-versatile` (~280 t/s)
  - `llama-3.1-8b-instant` (~560 t/s)
  - Llama 4 Scout (preview)
  - Qwen3-32B (preview)
- **OpenAI-compatible:** Yes — `https://api.groq.com/openai/v1`
- **Key prefix:** `gsk_`
- **Context window:** 131,072 tokens on production models
- **Rate limits:** Per-model per-minute and per-day token limits (generous for personal use)
- **Standout feature:** Lowest latency of any free provider — perfect for chat or interactive agents

---

## Google AI Studio

**URL:** https://aistudio.google.com

Google's developer portal for Gemini models.

- **Sign-up:** Google account, no credit card
- **Notable free models (May 2026):**
  - `gemini-3.1-flash-lite` (GA, 1M context, fastest)
  - `gemini-3-flash` (newer Flash model)
  - `gemini-2.5-flash` (still around, very reliable)
  - `gemini-2.5-flash-lite` (cheapest)
- **OpenAI-compatible:** Yes — `https://generativelanguage.googleapis.com/v1beta/openai/`
- **Key prefix:** `AIza`
- **Rate limits:** 5–15 requests/min and 100–1,500 requests/day depending on model; 250K tokens/min cap
- **Standout feature:** 1M-token context on all Gemini 3 Flash and 2.5 Flash models, free

> **Important policy change (April 1, 2026):** Google moved all **Pro** models behind the paid tier. `gemini-2.5-pro`, `gemini-3.1-pro`, and other Pro variants are **no longer free**. Only Flash and Flash-Lite remain on the free tier.

---

## Cerebras

**URL:** https://cloud.cerebras.ai

Fast inference on Cerebras wafer-scale chips. Among the fastest publicly available, especially for Llama models.

- **Sign-up:** Free account, no credit card
- **Notable free models:** `llama-3.3-70b`, `qwen-3-32b`, `qwen-3-235b-a22b`, `gpt-oss-120b`
- **OpenAI-compatible:** Yes — `https://api.cerebras.ai/v1`
- **Key prefix:** `csk-`
- **Rate limits:** 30 requests/min, 60K tokens/min, **1M tokens/day** — does not expire
- **Caveat:** Free tier has an 8,192-token context cap across all models. Paid tier unlocks full context.
- **Standout feature:** Among the fastest inference speeds available publicly. 2,600+ tokens/sec on Llama 4 Scout.

---

## SambaNova

**URL:** https://cloud.sambanova.ai

Free access to large open-weight models on Cerebras-like high-speed accelerators.

- **Sign-up:** Free account, no credit card
- **Notable free models:**
  - `Meta-Llama-4-Scout`, `Meta-Llama-4-Maverick`
  - `Meta-Llama-3.3-70B-Instruct`
  - `DeepSeek-V3.1` (up to 200 t/s)
  - `DeepSeek-R1-Distill-Llama-70B`
  - `QwQ-32B-Preview` (reasoning)
- **OpenAI-compatible:** Yes — `https://api.sambanova.ai/v1`
- **Key prefix:** `sn-`
- **Rate limits:** Per-minute and per-day limits, generous for personal use
- **Standout feature:** Free access to Llama 4 and DeepSeek-V3.1 at production-grade speeds (~294 t/s avg)

---

## OpenRouter

**URL:** https://openrouter.ai

A routing layer that aggregates many providers. Has a set of permanently free models tagged with `:free`.

- **Sign-up:** Free account, no credit card for free models
- **Notable free models:** Various Llama, Qwen, Gemma, DeepSeek, GLM and other `:free`-tagged endpoints (search for `:free` on their models page)
- **OpenAI-compatible:** Yes — `https://openrouter.ai/api/v1`
- **Key prefix:** `sk-or-`
- **Rate limits:** 20 requests/min, 200 requests/day on free tier (paid credits raise this significantly)
- **Standout feature:** Single API key works across dozens of models and providers — great for testing many models without juggling keys

---

## Hugging Face

**URL:** https://huggingface.co

Hosts hundreds of thousands of open-source models. The Inference API and serverless endpoints provide free access to many of them.

- **Sign-up:** Free account, no credit card
- **Notable free models:** Broad selection — Llama, Mistral, Falcon, BLOOM, and community models
- **OpenAI-compatible:** Partial — some endpoints are OpenAI-compatible (Inference Providers), others use their own format
- **Key prefix:** `hf_`
- **Rate limits:** Shared infrastructure on free tier — can be slow or rate-limited under load. Free monthly credits for Inference Providers.
- **Docs:** https://huggingface.co/docs/api-inference/index

---

## Cloudflare Workers AI

**URL:** https://developers.cloudflare.com/workers-ai

Run AI models on Cloudflare's edge network. Free tier included with any Cloudflare account.

- **Sign-up:** Free Cloudflare account, no credit card
- **Notable free models:** Llama 3.3, Mistral, DeepSeek, image-generation models (Stable Diffusion, Flux)
- **OpenAI-compatible:** Partial — has an OpenAI-compatible endpoint, but not all features are mapped
- **Free tier:** 10,000 neurons/day (neurons = compute units, not tokens)
- **Docs:** https://developers.cloudflare.com/workers-ai/models/

---

## Mistral AI

**URL:** https://console.mistral.ai

Mistral's own API platform. Offers a free experimentation tier.

- **Sign-up:** Free account, no credit card for free tier
- **Notable free models:** Mistral Small, Codestral, open-weight models
- **OpenAI-compatible:** Yes — `https://api.mistral.ai/v1`
- **Rate limits:** 1 request/sec, 500K tokens/min, 1B tokens/month on Experimentation tier
- **Docs:** https://docs.mistral.ai

---

## Cohere

**URL:** https://dashboard.cohere.com

Specializes in text generation, embeddings, and retrieval. Good free trial keys for non-commercial use.

- **Sign-up:** Free account, no credit card
- **Notable free models:** Command, Command R+, Embed, Rerank
- **OpenAI-compatible:** No — uses Cohere's own SDK (`pip install cohere`)
- **Rate limits:** 20 requests/min, 1,000 requests/month on trial key
- **Standout feature:** Best-in-class embeddings and reranking models on the free tier
- **Docs:** https://docs.cohere.com

---

## Tips for Using Free APIs

**Rotate providers to avoid rate limits**
Most tools (Aider, OpenCode, Gemini CLI, etc.) let you switch models mid-session. If you hit a rate limit on one provider, switch to another. Cerebras/Groq are great for speed; NVIDIA NIM for variety; SambaNova for the biggest open-weight models.

**Prefer OpenAI-compatible providers**
Tools like Aider, OpenCode, Codex, and Gemini CLI work out of the box with any OpenAI-compatible endpoint — just set `OPENAI_BASE_URL` and `OPENAI_API_KEY`.

**Watch for context window differences**
Free-tier context limits vary widely. DeepSeek V4 Flash (1M), Kimi K2.6 (256K), and Gemini Flash (1M) are among the largest. Cerebras free tier currently caps at 8,192 tokens — useful for speed but not for long context.

**Keep API keys out of code**
Store keys as environment variables or in config files outside your git repos. Never commit them. Many providers (NVIDIA, Groq, Gemini) will rotate / revoke keys that get leaked publicly.

**Pin to model IDs that exist on the model card page today**
Free hosted models churn quickly. Kimi K2.5 was retired this past spring; older Llama and Mistral models get rotated out too. If you see a 404 or 410, check the provider's model catalog page for a current ID.

---

## Datacenter Location vs. Model Origin

All providers listed in this guide operate US or EU-based datacenters — your prompts and data are processed on Western infrastructure. However, some popular models were created by Chinese organizations:

| Model | Made by | Served via (in this guide) | Data goes to |
|---|---|---|---|
| Kimi K2.6 | Moonshot AI (China) | NVIDIA NIM (US) | US |
| DeepSeek V4 / R1 | DeepSeek (China) | NVIDIA NIM, SambaNova (US) | US |
| Qwen 3 | Alibaba (China) | NVIDIA NIM, Cerebras, SambaNova (US) or Ollama (local) | Local or US |
| GLM-5.1 | Zhipu AI (China) | NVIDIA NIM (US) | US |

The model weights being of Chinese origin does not mean your data leaves US/EU infrastructure when using the providers above. The distinction matters if you are working with sensitive or proprietary code and your compliance team has views on either model lineage or data location.

**One exception to be aware of:** DeepSeek offers their own direct API at `api.deepseek.com`. That endpoint is hosted in China — data sent there goes to Chinese servers. The guides in this repo do not use that endpoint, but it is worth knowing if you encounter it elsewhere.
