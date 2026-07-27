# Free AI API Keys

A reference guide to AI providers offering free API access — no credit card required (unless noted). All entries have ongoing free tiers, not just one-time trial credits.

> **Note:** Rate limits, model availability, and free tier terms change frequently. Always verify current limits on the provider's pricing page before building on a free tier. Last reviewed July 2026.

> **Free tiers tightened noticeably in mid-2026.** Cerebras cut its free catalog from about a dozen models to two, OpenRouter dropped unfunded accounts to 50 requests/day, and Google's consumer CLI free tier moved to a much smaller compute-based quota. The numbers below reflect that; anything you read from early 2026 is likely too generous.

---

## Quick Reference

| Provider | Notable Free Models | OpenAI-Compatible | Credit Card Required |
|---|---|---|---|
| [NVIDIA NIM](#nvidia-nim) | DeepSeek V4 Flash/Pro, GLM-5.2, Qwen3.5, Kimi K2.6, Nemotron 3 Super 120B, 100+ more | Yes | No (phone verification) |
| [Groq](#groq) | GPT-OSS 120B/20B, Llama 3.3 70B, Llama 4 Scout | Yes | No |
| [Google AI Studio](#google-ai-studio) | Gemini 3.5 Flash, Gemini 3.1 Flash-Lite | Yes | No |
| [Cerebras](#cerebras) | GPT-OSS 120B, GLM-4.7 (only two on free tier) | Yes | No |
| [SambaNova](#sambanova) | Llama 4 Scout/Maverick, DeepSeek V3.1, QwQ-32B | Yes | No |
| [OpenRouter](#openrouter) | Many (`:free` tagged models) | Yes | No |
| [Hugging Face](#hugging-face) | Thousands of models | Partial | No |
| [Cloudflare Workers AI](#cloudflare-workers-ai) | Llama, Mistral, image-gen, and more | Partial | No (Cloudflare account) |
| [Mistral AI](#mistral-ai) | Mistral Small and others | Yes | No |
| [Cohere](#cohere) | Command, Embed, Rerank | No (own SDK) | No |

---

## NVIDIA NIM

**URL:** https://build.nvidia.com

The largest free catalog around — 120+ frontier hosted models via the NVIDIA Developer Program, of which roughly 99 are free-tier callable. As of July 2026 the lineup includes the DeepSeek V4 family, GLM-5.2, the Qwen3.5 series, Kimi K2.6, Llama 4, and many Nemotron variants. New models land fast: GLM-5.2 appeared about two weeks after its public release.

- **Sign-up:** Free NVIDIA Developer account. No credit card, but **phone verification is now required** at signup.
- **Notable free models:**
  - `deepseek-ai/deepseek-v4-flash` (284B MoE, 1M context, coding/agentic)
  - `deepseek-ai/deepseek-v4-pro` (1.6T MoE, 1M context)
  - `zai-org/glm-5.2` (current top-tier open-weight agentic model)
  - `qwen/qwen3-coder-480b-a35b-instruct` (code specialist)
  - `moonshotai/kimi-k2.6` (256K context, long-horizon agentic)
  - `nvidia/nemotron-3-super-120b-a12b` (NVIDIA's own flagship MoE)
  - `meta/llama-4-maverick-17b-128e-instruct`, `meta/llama-4-scout-17b-16e-instruct`
- **OpenAI-compatible:** Yes — `https://integrate.api.nvidia.com/v1`
- **Key prefix:** `nvapi-`
- **Rate limits:** ~40 requests/min, shared across all models rather than per-model
- **Context range:** 8K to 1M tokens depending on model
- **Standout feature:** One API key, 100+ models. Largest free hosted catalog available.
- **Guides in this repo:** [Aider + NVIDIA NIM](Aider_Setup_NVIDIA_NIM.md) | [OpenCode + NVIDIA NIM](OpenCode_Setup_NVIDIA_NIM.md)

> **The 403 / hang gotcha (important):** Some model families need a separate per-family registration before your standard `nvapi-` key can call them. The model shows in the catalog, but requests 404, 403, or hang indefinitely. Newer models like `kimi-k2.6` and `deepseek-v4-pro` are common culprits. **Fix:** open that model's page on build.nvidia.com and click **"Try API"** once to register your account for the family, then retry.

> **Heads-up on retired models:** Moonshot's Kimi K2.5 and K2 (Instruct/Thinking) were retired from NVIDIA NIM in April–May 2026. K2.6 is the current Kimi offering but has noticeably higher latency than the DeepSeek, GLM, or Qwen alternatives.

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
- **Rate limits (July 2026):** Roughly **30 requests/min, 1,000 requests/day** per model on the default free tier, with per-model token caps (`llama-3.3-70b-versatile` at 12K TPM / 100K TPD, `openai/gpt-oss-120b` at 8K TPM / 200K TPD). Llama 4 Maverick runs at half quota (15 RPM / 500 RPD).
- **Important:** limits are enforced **per organization, not per API key**. Extra keys under the same org share one bucket.
- **Standout feature:** Lowest latency of any free provider — perfect for chat or interactive agents. Every model is available on the free tier; you are gated by rate limits only, with no credits system and no per-token charge.

> **Cheap upgrade:** adding a credit card with zero minimum spend unlocks up to 10x the free rate limits plus a 25% token discount. Worth knowing if 30 RPM is your only blocker.

---

## Google AI Studio

**URL:** https://aistudio.google.com

Google's developer portal for Gemini models.

- **Sign-up:** Google account, no credit card
- **Notable free models (July 2026):**
  - `gemini-3.5-flash` (current flagship Flash, 15 RPM / 1,500 RPD)
  - `gemini-3.1-flash-lite` (1M context, fastest and cheapest)
- **OpenAI-compatible:** Yes — `https://generativelanguage.googleapis.com/v1beta/openai/`
- **Key prefix:** `AIza`
- **Rate limits:** 15 requests/min and up to 1,500 requests/day on Gemini 3.5 Flash; lower on some models
- **Standout feature:** 1M-token context on the free Flash models, and the free tier is permanent rather than trial-based

> **Pro models are paid (since April 1, 2026).** `gemini-3.1-pro`, `gemini-2.5-pro`, and the image-generation models are paid-only. Only Flash and Flash-Lite remain free.

> **Privacy caveat:** Google may use free-tier inputs and outputs to improve its models. Commercial use is allowed, but if your prompts are sensitive, use the paid tier or Vertex AI, neither of which trains on your data.

> **Not the same as the CLI.** The Google AI Studio *API* free tier is alive and well. It was the consumer **Gemini CLI** that shut down on June 18, 2026 — see the [Antigravity CLI guide](Antigravity_CLI_Setup_Windows_11.md).

---

## Cerebras

**URL:** https://cloud.cerebras.ai

Fast inference on Cerebras wafer-scale chips. Among the fastest publicly available, especially for Llama models.

- **Sign-up:** Free account, no credit card
- **Notable free models:** `gpt-oss-120b` and `glm-4.7` — **that is the whole free catalog now**
- **OpenAI-compatible:** Yes — `https://api.cerebras.ai/v1`
- **Key prefix:** `csk-`
- **Rate limits:** 5 requests/min, 30K tokens/min, **1M tokens/day** — resets daily, does not expire
- **Caveat:** Free tier caps context at 8,192 tokens on these models. Paid unlocks full context (up to 131K).
- **Standout feature:** Still among the fastest inference available publicly, at 2,600+ tokens/sec.

> **Major downgrade (May 31, 2026):** Cerebras collapsed its free catalog from roughly a dozen models to **two**, and cut free-tier throughput to 5 RPM / 30K TPM. The Llama 3.3 70B, Qwen3 32B, and Qwen3 235B free endpoints this guide previously listed are gone. The 1M tokens/day headline number survived, but you can no longer spend it quickly or on the model of your choice.

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

- **Sign-up:** Free account, no credit card needed to sign up or to call `:free` models at a $0 balance
- **Notable free models:** 18–28 `:free`-tagged endpoints at any given time, including DeepSeek R1, Llama 3.3 70B, Qwen3 Coder 480B (262K context), Gemma 3, and Gemini Flash
- **OpenAI-compatible:** Yes — `https://openrouter.ai/api/v1`
- **Key prefix:** `sk-or-`
- **Rate limits:** 20 requests/min always, plus **50 requests/day on an unfunded account**
- **Standout feature:** Single API key works across dozens of models and providers — great for testing many models without juggling keys

> **The $10 lever:** buying $10 in credits **once** raises the daily cap from 50 to 1,000 requests/day permanently, and the credits never expire. This is the single best value unlock among the providers in this guide. (Minimum purchase is $5, but $10 is the threshold that moves the daily limit.)

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
Most tools (Aider, OpenCode, etc.) let you switch models mid-session. If you hit a rate limit on one provider, switch to another. Groq is great for speed; NVIDIA NIM for variety and context length; SambaNova for the biggest open-weight models.

**Prefer OpenAI-compatible providers**
Tools like Aider, OpenCode, and Codex work out of the box with any OpenAI-compatible endpoint — just set `OPENAI_BASE_URL` and `OPENAI_API_KEY`.

**Watch for context window differences**
Free-tier context limits vary widely. DeepSeek V4 Flash/Pro (1M), Gemini Flash (1M), and Kimi K2.6 (256K) are among the largest. Cerebras free tier caps at 8,192 tokens, which is fine for quick chat but useless for feeding it a repo.

**Check the daily cap, not just the per-minute one**
The requests-per-minute number is rarely what stops you. The daily ceiling is: OpenRouter at 50/day unfunded, Groq at ~1,000/day per model, Google at 1,500/day. Budget against that.

**Keep API keys out of code**
Store keys as environment variables or in config files outside your git repos. Never commit them. Many providers (NVIDIA, Groq, Gemini) will rotate / revoke keys that get leaked publicly.

**Pin to model IDs that exist on the model card page today**
Free hosted models churn quickly. Kimi K2.5 was retired in spring 2026, and Cerebras dropped most of its free catalog on May 31. If you see a 404 or 410, check the provider's model catalog page for a current ID. On NVIDIA NIM specifically, a 403 or an indefinite hang usually means the model family needs a one-time "Try API" registration rather than a dead ID.

**Assume free tiers shrink**
Every provider in this guide that changed its free tier in 2026 made it smaller, not larger. If a free tier is load-bearing for something you care about, have a second provider configured before you need it.

---

## Datacenter Location vs. Model Origin

All providers listed in this guide operate US or EU-based datacenters — your prompts and data are processed on Western infrastructure. However, some popular models were created by Chinese organizations:

| Model | Made by | Served via (in this guide) | Data goes to |
|---|---|---|---|
| Kimi K2.6 | Moonshot AI (China) | NVIDIA NIM (US) | US |
| DeepSeek V4 / R1 | DeepSeek (China) | NVIDIA NIM, SambaNova (US) | US |
| Qwen 3 / 3.5 | Alibaba (China) | NVIDIA NIM, SambaNova (US) or Ollama (local) | Local or US |
| GLM-4.7 / GLM-5.2 | Zhipu AI (China) | NVIDIA NIM, Cerebras (US) | US |

The model weights being of Chinese origin does not mean your data leaves US/EU infrastructure when using the providers above. The distinction matters if you are working with sensitive or proprietary code and your compliance team has views on either model lineage or data location.

**One exception to be aware of:** DeepSeek offers their own direct API at `api.deepseek.com`. That endpoint is hosted in China — data sent there goes to Chinese servers. The guides in this repo do not use that endpoint, but it is worth knowing if you encounter it elsewhere.
