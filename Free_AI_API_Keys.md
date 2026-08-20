# Free AI API Keys

A reference guide to AI providers offering free API access, no credit card required (unless noted). All entries have ongoing free tiers, not just one-time trial credits.

> **Note:** Rate limits, model availability, and free tier terms change frequently. Always verify current limits on the provider's pricing page before building on a free tier. Last reviewed August 2026, with NVIDIA and Groq model IDs verified by live API call on 2026-08-20.

> **Free tiers kept shrinking through 2026.** Cerebras removed its open free tier entirely on August 17, Groq dropped every Llama chat model, OpenRouter holds unfunded accounts at 50 requests/day, and Google's consumer CLI free tier moved to a much smaller compute-based quota. Anything you read about these providers from early 2026 is too generous.

---

## Quick Reference

| Provider | Notable Free Models | OpenAI-Compatible | Credit Card Required |
|---|---|---|---|
| [NVIDIA NIM](#nvidia-nim) | Kimi K3, DeepSeek V4 Flash 0731, GLM-5.2, MiniMax M3, Nemotron 3, 100+ more | Yes | No (phone verification) |
| [Groq](#groq) | GPT-OSS 120B/20B, Qwen3.6 27B, Compound | Yes | No |
| [Google AI Studio](#google-ai-studio) | Gemini 3.7 / 3.6 / 3.5 Flash, Flash-Lite | Yes | No |
| [SambaNova](#sambanova) | MiniMax M2.7, DeepSeek V3.1, Llama 3.3 70B, GPT-OSS 120B | Yes | No |
| [OpenRouter](#openrouter) | Many (`:free` tagged models) | Yes | No |
| [Hugging Face](#hugging-face) | Thousands of models | Partial | No |
| [Cloudflare Workers AI](#cloudflare-workers-ai) | Llama, Mistral, image-gen, and more | Partial | No (Cloudflare account) |
| [Mistral AI](#mistral-ai) | Mistral Small and others | Yes | No |
| [Cohere](#cohere) | Command, Embed, Rerank | No (own SDK) | No |
| ~~Cerebras~~ | **[Free tier ended 2026-08-17](#cerebras)** | Yes | **Yes, now required** |

---

## NVIDIA NIM

**URL:** https://build.nvidia.com

The largest free catalog around. A live call to `GET /v1/models` on 2026-08-20 returned **103 models**, spanning chat, vision, embedding, and reranking. The current lineup includes Kimi K3, DeepSeek V4 Flash 0731, GLM-5.2, MiniMax M3, Step 3.7 Flash, the Nemotron 3 family, and GPT-OSS. New models land fast: Kimi K3 appeared on NIM within weeks of Moonshot publishing the weights.

- **Sign-up:** Free NVIDIA Developer account. No credit card, but **phone verification is required** at signup.
- **Notable free models (verified callable 2026-08-20):**
  - `moonshotai/kimi-k3` (2.8T params, 1M context, multimodal, the strongest free model here)
  - `deepseek-ai/deepseek-v4-flash-0731` (284B MoE / 13B active, 1M context, fastest good coder)
  - `z-ai/glm-5.2` (744B MoE / 40B active, 1M context, MIT-licensed agentic coder)
  - `minimaxai/minimax-m3` (428B MoE / 23B active, 1M context, multimodal)
  - `stepfun-ai/step-3.7-flash` (198B MoE / 11B active, 256K context, vision)
  - `nvidia/nemotron-3-super-120b-a12b` (120B / 12B active, 1M context)
  - `nvidia/nemotron-3-ultra-550b-a55b` (550B / 55B active, 262K context)
  - `nvidia/nemotron-3.5-lightning-30b-a3b` (small, fast, low-latency)
  - `openai/gpt-oss-20b` (`openai/gpt-oss-120b` also exists, but needed the "Try API" registration below on a fresh key)
- **OpenAI-compatible:** Yes — `https://integrate.api.nvidia.com/v1`
- **Key prefix:** `nvapi-`
- **Rate limits:** ~40 requests/min, shared across all models rather than per-model. You can apply for a 200 RPM increase through the NVIDIA Developer Forums.
- **Context range:** 4K to 1M tokens depending on model
- **Standout feature:** One API key, 100+ models. Largest free hosted catalog available anywhere.
- **Guides in this repo:** [Aider + NVIDIA NIM](Aider_Setup_NVIDIA_NIM.md) | [OpenCode + NVIDIA NIM](OpenCode_Setup_NVIDIA_NIM.md)

### Reading NVIDIA's error codes (the 403 vs 410 diagnostic)

NVIDIA's failure modes look similar from the outside but mean very different things. This table is the fastest way to tell them apart, and it is worth internalizing, because the wrong diagnosis sends you down a long dead end.

| Response | What it actually means | Fix |
|---|---|---|
| `410 Gone` with an end-of-life date | The model is genuinely retired. Note that this is returned **even when your key is invalid**, because the EOL check runs before authentication. | Pick a current model ID |
| `404 page not found` (bare) | The model slug does not exist. Usually a typo or a wrong org prefix, for example `zai-org/glm-5.2` instead of `z-ai/glm-5.2` | Check the URL slug on the model's page at build.nvidia.com |
| `404` with `Function '<uuid>': Not found for account` | The model exists, but **your account is not registered for it** | Open its page on build.nvidia.com and click **"Try API"** once, then retry |
| `403 Authorization failed` on **every** live model | Your key is invalid or **expired** | Generate a new key |
| Request hangs indefinitely, no response at all | Same per-account registration gap as the `404 Function` case, just failing silently instead | Click **"Try API"** on that model's page |

> **The key-expiry trap (new for August 2026):** NVIDIA keys now carry a time-to-live that you select when you generate them, from one hour up to "never expires." One year is a common choice. Because a dead key returns `403` on live models while retired models still return their `410` notice, an expired key looks exactly like "every model I want got deprecated at once." If you see `403 Authorization failed` across several unrelated models, the problem is your key, not the catalog.

> **Recently retired on NIM (confirmed by live `410` responses):** `deepseek-ai/deepseek-v4-flash` and `deepseek-ai/deepseek-v4-pro` (both EOL 2026-08-07), `meta/llama-4-maverick-17b-128e-instruct` (2026-07-27), `qwen/qwen3-coder-480b-a35b-instruct` (2026-06-11), `moonshotai/kimi-k2-instruct` (2026-05-12). Kimi K2.5 no longer resolves at all. NVIDIA appears to have cleared capacity for Kimi K3 and the updated DeepSeek build.

> **Expect to hit the registration gate on a fresh key.** This is not a rare edge case. Testing a brand-new key on 2026-08-20, four of the catalog's most popular models were unreachable on a key not yet registered for them: `openai/gpt-oss-120b`, `google/gemma-4-31b-it`, and `meta/llama-3.3-70b-instruct` each hung for a full four minutes with no response at all, and `moonshotai/kimi-k2.6` returned `Function ... Not found for account`. All of them are listed in `/v1/models`, so the catalog is not a reliable guide to what your key can actually call. Click "Try API" once on each model page you plan to use, before you wire it into a config. (The hang and the `Not found for account` error were both reproduced directly; the "Try API" step is NVIDIA's documented remedy for them.)

---

## Groq

**URL:** https://console.groq.com

Extremely fast inference on custom LPU hardware, still the lowest-latency free API available. The catalog is now much narrower than it used to be.

- **Sign-up:** Free account, no credit card
- **Full catalog as of 2026-08-20 (13 models, verified by live API call):**
  - `openai/gpt-oss-120b` (131K context)
  - `openai/gpt-oss-20b` (131K context, fastest production model)
  - `openai/gpt-oss-safeguard-20b` (131K context)
  - `qwen/qwen3.6-27b` (131K context)
  - `groq/compound`, `groq/compound-mini` (131K context, Groq's agentic tool-using systems)
  - `allam-2-7b` (4K context)
  - `whisper-large-v3`, `whisper-large-v3-turbo` (speech-to-text)
  - `canopylabs/orpheus-v1-english`, `canopylabs/orpheus-arabic-saudi` (text-to-speech)
  - `meta-llama/llama-prompt-guard-2-22m`, `meta-llama/llama-prompt-guard-2-86m` (safety classifiers, not chat models)
- **OpenAI-compatible:** Yes — `https://api.groq.com/openai/v1`
- **Key prefix:** `gsk_`
- **Rate limits (August 2026):** 30 requests/min and 1,000 requests/day on the `openai/gpt-oss` models, at 8K TPM / 200K TPD. Other models vary; Whisper gets 2,000 audio requests/day.
- **Important:** limits are enforced **per organization, not per API key**. Extra keys under the same org share one bucket.
- **Standout feature:** Lowest latency of any free provider. Every listed model is available on the free tier, gated by rate limits only, with no credits system and no per-token charge.

> **All Llama chat models are gone.** `llama-3.3-70b-versatile`, `llama-3.1-8b-instant`, and the Llama 4 previews have been removed from Groq. The only remaining Meta entries are the two `llama-prompt-guard-2` classifiers, which cannot hold a conversation. If you have a Groq config from earlier in 2026, it is almost certainly pointing at a dead model. `openai/gpt-oss-20b` is the closest replacement for the old 8B-instant slot, and `openai/gpt-oss-120b` for the 70B slot.

> **Cheap upgrade:** adding a credit card with zero minimum spend unlocks up to 10x the free rate limits plus a 25% token discount. Worth knowing if 30 RPM is your only blocker.

---

## Google AI Studio

**URL:** https://aistudio.google.com

Google's developer portal for Gemini models.

- **Sign-up:** Google account, no credit card
- **Notable free models (August 2026):**
  - `gemini-3.7-flash` (current flagship Flash)
  - `gemini-3.6-flash`, `gemini-3.5-flash` (15 RPM / 1,500 RPD)
  - `gemini-3.5-flash-lite`, `gemini-3.1-flash-lite` (1M context, fastest and cheapest)
  - The legacy 2.5 and 2.0 Flash families are also still free at the API level
- **OpenAI-compatible:** Yes — `https://generativelanguage.googleapis.com/v1beta/openai/`
- **Key prefix:** `AIza`
- **Rate limits:** 15 requests/min and up to 1,500 requests/day on Gemini 3.5 Flash, lower on some models. RPD resets at midnight Pacific.
- **Quota is per project, not per key.** Creating a second API key in the same project does not double your allowance.
- **Standout feature:** 1M-token context on the free Flash models, and the free tier is permanent rather than trial-based

> **Pro models are paid (since April 1, 2026).** The Pro series and the image-generation models are paid-only. Only Flash and Flash-Lite remain free.

> **Privacy caveat:** Google may use free-tier inputs and outputs to improve its models. Commercial use is allowed, but if your prompts are sensitive, use the paid tier or Vertex AI, neither of which trains on your data.

> **Not the same as the CLI.** The Google AI Studio *API* free tier is alive and well. It was the consumer **Gemini CLI** that shut down on June 18, 2026, see the [Antigravity CLI guide](Antigravity_CLI_Setup_Windows_11.md).

---

## SambaNova

**URL:** https://cloud.sambanova.ai

Free access to large open-weight models on high-speed reconfigurable dataflow accelerators.

- **Sign-up:** Free Developer tier, no credit card, no payment method required
- **Notable free models (August 2026):**
  - `MiniMax-M2.7` (192K context)
  - `DeepSeek-V3.1` (128K context, up to ~200 t/s)
  - `Meta-Llama-3.3-70B-Instruct` (128K context)
  - `gpt-oss-120b` (128K context)
  - Preview: `DeepSeek-V3.2` (32K), `gemma-4-31B-it` (128K, text + image + video)
- **OpenAI-compatible:** Yes — `https://api.sambanova.ai/v1`
- **Rate limits:** Developer tier accounts are capped at 20M tokens/day across all models, with per-model RPM and RPD limits on top
- **Standout feature:** One of the few remaining places to get Llama 3.3 70B free, now that Groq has dropped it. Production-grade speeds, around 294 t/s average.

> **The Llama 4 and QwQ models previously listed here are gone.** SambaNova's current catalog is the six models above. MiniMax M2.7 is the closest thing to a flagship on the free tier.

---

## OpenRouter

**URL:** https://openrouter.ai

A routing layer that aggregates many providers. Has a set of permanently free models tagged with `:free`.

- **Sign-up:** Free account, no credit card needed to sign up or to call `:free` models at a $0 balance
- **Notable free models:** 14–28 `:free`-tagged endpoints at any given time, including Nemotron 3 Super, DeepSeek, Llama, Gemma, and Gemini Flash
- **OpenAI-compatible:** Yes — `https://openrouter.ai/api/v1`
- **Key prefix:** `sk-or-`
- **Rate limits:** 20 requests/min always, plus **50 requests/day on an unfunded account**
- **Standout feature:** A single API key works across dozens of models and providers, which makes it the easiest way to test many models without juggling keys

> **The $10 lever:** buying $10 in credits **once** raises the daily cap from 50 to 1,000 requests/day permanently, and the credits never expire. This is still the single best value unlock among the providers in this guide. (Minimum purchase is $5, but $10 is the threshold that moves the daily limit.) The 20 RPM cap does not change; only the daily ceiling moves.

> Guides quoting "200 requests/day" for OpenRouter's free tier are out of date. The current tiers are 50/day and 1,000/day.

---

## Hugging Face

**URL:** https://huggingface.co

Hosts hundreds of thousands of open-source models. The Inference API and serverless endpoints provide free access to many of them.

- **Sign-up:** Free account, no credit card
- **Notable free models:** Broad selection, including Llama, Mistral, Falcon, and community models
- **OpenAI-compatible:** Partial. Some endpoints are OpenAI-compatible (Inference Providers), others use their own format
- **Key prefix:** `hf_`
- **Rate limits:** Shared infrastructure on the free tier, so it can be slow or rate-limited under load. Free monthly credits for Inference Providers.
- **Docs:** https://huggingface.co/docs/api-inference/index

---

## Cloudflare Workers AI

**URL:** https://developers.cloudflare.com/workers-ai

Run AI models on Cloudflare's edge network. Free tier included with any Cloudflare account.

- **Sign-up:** Free Cloudflare account, no credit card
- **Notable free models:** Llama 3.3, Mistral, DeepSeek, image-generation models (Stable Diffusion, Flux)
- **OpenAI-compatible:** Partial. Has an OpenAI-compatible endpoint, but not all features are mapped
- **Free tier:** 10,000 neurons/day (neurons are compute units, not tokens)
- **Docs:** https://developers.cloudflare.com/workers-ai/models/

---

## Mistral AI

**URL:** https://console.mistral.ai

Mistral's own API platform. Offers a free experimentation tier.

- **Sign-up:** Free account, no credit card for free tier
- **Notable free models:** Mistral Small, Codestral, open-weight models
- **OpenAI-compatible:** Yes — `https://api.mistral.ai/v1`
- **Rate limits:** 1 request/sec, 500K tokens/min, 1B tokens/month on the Experimentation tier
- **Docs:** https://docs.mistral.ai

---

## Cohere

**URL:** https://dashboard.cohere.com

Specializes in text generation, embeddings, and retrieval. Good free trial keys for non-commercial use.

- **Sign-up:** Free account, no credit card
- **Notable free models:** Command, Command R+, Embed, Rerank
- **OpenAI-compatible:** No, uses Cohere's own SDK (`pip install cohere`)
- **Rate limits:** 20 requests/min, 1,000 requests/month on a trial key
- **Standout feature:** Best-in-class embeddings and reranking models on the free tier
- **Docs:** https://docs.cohere.com

---

## Retired: No Longer Free

These providers were listed in earlier versions of this guide and no longer meet its criteria.

### Cerebras

**Status:** Free tier ended **2026-08-17**.

Cerebras had one of the better deals here: 1M tokens/day at 2,600+ tokens/sec on wafer-scale hardware. That shrank on 2026-05-31 (catalog cut from about a dozen models to two, throughput cut to 5 RPM / 30K TPM), and then ended outright. As of July 21, 2026 new signups got a Free Trial instead of a free tier, and on August 17 existing accounts were migrated onto it.

What replaced it: **$5 in one-time credits, granted only after you add a verified payment method, expiring 30 days after issue.** There is no renewing allowance and no always-free per-model quota. `zai-glm-4.7`, one of the two remaining free models, was also scheduled for deprecation on the same date.

If you were using Cerebras for speed, the nearest free substitutes are Groq (`openai/gpt-oss-20b`) for raw latency and SambaNova for large-model throughput.

### xAI / Grok

**Status:** Never had an ongoing free tier, so it does not qualify.

New accounts receive $25 in promotional credits that expire after 30 days, then it is pay-per-token. The $150/month data-sharing credit program is no longer reliably available to new developers. xAI also retired eight models in May 2026, including `grok-3`, `grok-4-fast`, `grok-4-1-fast-reasoning`, and `grok-code-fast-1`; configs pointing at those will fail, and the migration target (`grok-4.3`) costs roughly 6x more per input token than the budget models it replaced.

xAI's August 2026 [Grok Bot](https://x.ai/news/introducing-grok-bot) release is a paid agent product ($300/mo SuperGrok Heavy, $200/mo Cursor Ultra, or $120/seat/mo Cursor Teams Premium), closed source, and desktop and iOS rather than CLI, so it is out of scope for this repo on every axis.

---

## Tips for Using Free APIs

**Verify model IDs before you trust a guide, including this one**
Free hosted models churn fast enough that any written guide is partly stale the week it is published. The cheapest check is a one-token request against the endpoint. A `410` tells you the model is retired and usually names the EOL date; a `404` tells you the slug is wrong. Both take a second to test and save a long debugging session.

**Rotate providers to avoid rate limits**
Most tools (Aider, OpenCode, and similar) let you switch models mid-session. If you hit a rate limit on one provider, switch to another. Groq is best for speed, NVIDIA NIM for variety and context length, SambaNova for the biggest open-weight models.

**Prefer OpenAI-compatible providers**
Tools like Aider, OpenCode, and Codex work out of the box with any OpenAI-compatible endpoint. Set `OPENAI_BASE_URL` and `OPENAI_API_KEY` and you are done.

**Watch for context window differences**
Free-tier context limits vary widely. Kimi K3, DeepSeek V4 Flash 0731, GLM-5.2, MiniMax M3, Nemotron 3 Super, and Gemini Flash all reach 1M. Step 3.7 Flash and Nemotron 3 Ultra sit around 256K. Groq caps at 131K.

**Check the daily cap, not just the per-minute one**
The requests-per-minute number is rarely what stops you. The daily ceiling is: OpenRouter at 50/day unfunded, Groq at 1,000/day on the GPT-OSS models, Google at 1,500/day, SambaNova at 20M tokens/day. Budget against that.

**Keep API keys out of code**
Store keys as environment variables or in config files outside your git repos. Never commit them. Many providers (NVIDIA, Groq, Gemini) will rotate or revoke keys that get leaked publicly. Note that OpenCode's `opencode.json` stores keys in plain text, so keep that file out of any repo.

**Track your key expiry dates**
NVIDIA keys now expire on a schedule you choose at generation time. Set a calendar reminder, or pick "never expires" if the account is only used for personal projects. An expired key produces a `403` that reads like a model problem rather than an auth problem.

**Assume free tiers shrink**
Every provider in this guide that changed its free tier during 2026 made it smaller, not larger, and two of them (Cerebras, Groq) did so with little warning. If a free tier is load-bearing for something you care about, have a second provider configured before you need it.

---

## Datacenter Location vs. Model Origin

All providers listed in this guide operate US or EU-based datacenters, so your prompts and data are processed on Western infrastructure. However, several of the strongest open-weight models were created by Chinese organizations:

| Model | Made by | Served via (in this guide) | Data goes to |
|---|---|---|---|
| Kimi K3 / K2.6 | Moonshot AI (China) | NVIDIA NIM (US) | US |
| DeepSeek V4 Flash 0731 / V3.1 | DeepSeek (China) | NVIDIA NIM, SambaNova (US) | US |
| Qwen 3.5 / 3.6 | Alibaba (China) | Groq, NVIDIA NIM (US) or Ollama (local) | Local or US |
| GLM-5.2 | Zhipu AI / Z.ai (China) | NVIDIA NIM (US) | US |
| MiniMax M3 / M2.7 | MiniMax (China) | NVIDIA NIM, SambaNova (US) | US |
| Step 3.7 Flash | StepFun (China) | NVIDIA NIM (US) | US |

The model weights being of Chinese origin does not mean your data leaves US/EU infrastructure when using the providers above. The distinction matters if you are working with sensitive or proprietary code and your compliance team has views on either model lineage or data location.

**One exception to be aware of:** DeepSeek offers their own direct API at `api.deepseek.com`. That endpoint is hosted in China, so data sent there goes to Chinese servers. The guides in this repo do not use that endpoint, but it is worth knowing if you encounter it elsewhere.
