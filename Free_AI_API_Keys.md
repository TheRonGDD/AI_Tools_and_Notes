# Free AI API Keys

A reference guide to AI providers offering free API access — no credit card required (unless noted). All entries have ongoing free tiers, not just one-time trial credits.

> **Note:** Rate limits, model availability, and free tier terms change frequently. Always verify current limits on the provider's pricing page before building on a free tier.

---

## Quick Reference

| Provider | Free Models | OpenAI-Compatible | Credit Card Required |
|---|---|---|---|
| [NVIDIA NIM](#nvidia-nim) | Kimi K2.5, Llama, Mistral, and more | Yes | No |
| [Groq](#groq) | Llama 3, Gemma, Mixtral, and more | Yes | No |
| [Google AI Studio](#google-ai-studio) | Gemini 2.0 Flash, Gemini 1.5 Pro, and more | Yes (OpenAI compat endpoint) | No |
| [Cerebras](#cerebras) | Llama 3.1, Llama 3.3 | Yes | No |
| [SambaNova](#sambanova) | Llama 3.1, DeepSeek R1, and more | Yes | No |
| [OpenRouter](#openrouter) | Many (`:free` tagged models) | Yes | No |
| [Hugging Face](#hugging-face) | Thousands of models | Partial | No |
| [Cloudflare Workers AI](#cloudflare-workers-ai) | Llama, Mistral, and more | Partial | No (Cloudflare account) |
| [Mistral AI](#mistral-ai) | Mistral Small and others | Yes | No |
| [Cohere](#cohere) | Command R, Embed, and more | No (own SDK) | No |

---

## NVIDIA NIM

**URL:** https://build.nvidia.com

Free access to a large catalog of hosted models via the NVIDIA Developer Program.

- **Sign-up:** Free NVIDIA Developer account, no credit card
- **Notable free models:** Kimi K2.5, Llama 3.1 405B, Mistral Large, Phi-3, Gemma 2
- **OpenAI-compatible:** Yes — `https://integrate.api.nvidia.com/v1`
- **Key prefix:** `nvapi-`
- **Rate limits:** Per-model, varies — check each model's page
- **Guides in this repo:** [Aider + Kimi K2.5](Aider_Setup_with_Kimi_K2.5_NVIDIA.md) · [OpenCode + Kimi K2.5](Opencode_with_Kimi_K2.5_NVIDIA.md)

---

## Groq

**URL:** https://console.groq.com

Extremely fast inference (runs on custom LPU hardware). One of the fastest free APIs available.

- **Sign-up:** Free account, no credit card
- **Notable free models:** Llama 3.3 70B, Llama 3.1 8B, Gemma 2 9B, Mixtral 8x7B, DeepSeek R1
- **OpenAI-compatible:** Yes — `https://api.groq.com/openai/v1`
- **Key prefix:** `gsk_`
- **Rate limits:** Per-model per-minute and per-day token limits (generous for personal use)
- **Standout feature:** Very low latency — noticeably faster than most providers

---

## Google AI Studio

**URL:** https://aistudio.google.com

Google's developer portal for Gemini models. Generous free tier, especially for Gemini Flash.

- **Sign-up:** Google account, no credit card
- **Notable free models:** Gemini 2.0 Flash, Gemini 1.5 Flash, Gemini 1.5 Pro (limited)
- **OpenAI-compatible:** Yes — `https://generativelanguage.googleapis.com/v1beta/openai/`
- **Key prefix:** `AIza`
- **Rate limits:** 15 requests/min and 1,500 requests/day on free tier for Flash models
- **Standout feature:** Gemini 2.0 Flash has a 1M token context window on the free tier

---

## Cerebras

**URL:** https://cloud.cerebras.ai

Fast inference on Cerebras wafer-scale chips. Competitive speeds with Groq.

- **Sign-up:** Free account, no credit card
- **Notable free models:** Llama 3.1 8B, Llama 3.3 70B
- **OpenAI-compatible:** Yes — `https://api.cerebras.ai/v1`
- **Key prefix:** `csk-`
- **Rate limits:** Daily token limits on free tier
- **Standout feature:** Among the fastest inference speeds available publicly

---

## SambaNova

**URL:** https://cloud.sambanova.ai

Free access to large models including DeepSeek R1 full-size.

- **Sign-up:** Free account, no credit card
- **Notable free models:** Llama 3.1 405B, Llama 3.3 70B, DeepSeek R1 671B
- **OpenAI-compatible:** Yes — `https://api.sambanova.ai/v1`
- **Key prefix:** `sn-`
- **Rate limits:** Per-minute and per-day limits
- **Standout feature:** Hosts DeepSeek R1 671B (full-size) for free — rare at this scale

---

## OpenRouter

**URL:** https://openrouter.ai

A routing layer that aggregates many providers. Has a set of permanently free models tagged with `:free`.

- **Sign-up:** Free account, no credit card for free models
- **Notable free models:** Llama 3.1 8B, Gemma 2 9B, Mistral 7B, Qwen and more (search for `:free` on their models page)
- **OpenAI-compatible:** Yes — `https://openrouter.ai/api/v1`
- **Key prefix:** `sk-or-`
- **Rate limits:** 20 requests/min, 200 requests/day on free tier
- **Standout feature:** Single API key works across dozens of models and providers

---

## Hugging Face

**URL:** https://huggingface.co

Hosts thousands of open-source models. The Inference API provides free access to many of them.

- **Sign-up:** Free account, no credit card
- **Notable free models:** Broad selection — Llama, Mistral, Falcon, BLOOM, and community models
- **OpenAI-compatible:** Partial — some endpoints are OpenAI-compatible, others use their own format
- **Key prefix:** `hf_`
- **Rate limits:** Shared infrastructure on free tier — can be slow or rate limited under load
- **Docs:** https://huggingface.co/docs/api-inference/index

---

## Cloudflare Workers AI

**URL:** https://developers.cloudflare.com/workers-ai

Run AI models on Cloudflare's edge network. Free tier included with any Cloudflare account.

- **Sign-up:** Free Cloudflare account, no credit card
- **Notable free models:** Llama 3, Mistral 7B, Phi-2, image generation models
- **OpenAI-compatible:** Partial — REST API but not fully OpenAI-compatible
- **Free tier:** 10,000 neurons/day (neurons = compute units, not tokens)
- **Docs:** https://developers.cloudflare.com/workers-ai/models/

---

## Mistral AI

**URL:** https://console.mistral.ai

Mistral's own API platform. Offers a free tier for their smaller models.

- **Sign-up:** Free account, no credit card for free tier
- **Notable free models:** Mistral Small, open-weight models
- **OpenAI-compatible:** Yes — `https://api.mistral.ai/v1`
- **Key prefix:** varies
- **Rate limits:** Limited on free tier — check their pricing page for current limits
- **Docs:** https://docs.mistral.ai

---

## Cohere

**URL:** https://dashboard.cohere.com

Specializes in text generation, embeddings, and retrieval. Good free tier for non-commercial use.

- **Sign-up:** Free account, no credit card
- **Notable free models:** Command R, Command R+, Embed, Rerank
- **OpenAI-compatible:** No — uses Cohere's own SDK (`pip install cohere`)
- **Rate limits:** 20 requests/min, 1,000 requests/month on trial key
- **Standout feature:** Best-in-class embeddings and reranking models on the free tier
- **Docs:** https://docs.cohere.com

---

## Tips for Using Free APIs

**Rotate providers to avoid rate limits**
Most tools (Aider, OpenCode, etc.) let you switch models mid-session. If you hit a rate limit on one provider, switch to another.

**Prefer OpenAI-compatible providers**
Tools like Aider, OpenCode, and Codex work out of the box with any OpenAI-compatible endpoint — just set `OPENAI_BASE_URL` and `OPENAI_API_KEY`.

**Watch for context window differences**
Free tier models vary widely in context window size. Kimi K2.5 (262k) and Gemini Flash (1M) are among the largest available for free.

**Keep API keys out of code**
Store keys as environment variables or in config files outside your git repos. Never commit them.

**Datacenter location vs. model origin**
All providers listed in this guide operate US or EU-based datacenters — your prompts and data are processed on Western infrastructure. However, some popular models were created by Chinese organizations:

| Model | Made by | Served via (in this guide) | Data goes to |
|---|---|---|---|
| Kimi K2.5 | Moonshot AI (China) | NVIDIA NIM (US) | US |
| DeepSeek R1 | DeepSeek (China) | SambaNova, Groq (US) | US |
| Qwen | Alibaba (China) | Ollama (local) or US providers | Local or US |

The model weights being of Chinese origin does not mean your data leaves US/EU infrastructure when using the providers above. The distinction matters if you are working with sensitive or proprietary code.

**One exception to be aware of:** DeepSeek offers their own direct API at `api.deepseek.com`. That endpoint is hosted in China — data sent there goes to Chinese servers. The guides in this repo do not use that endpoint, but it is worth knowing if you encounter it elsewhere.
