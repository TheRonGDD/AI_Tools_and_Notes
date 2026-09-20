# Free AI API Keys

A reference guide to AI providers offering free API access, no credit card required (unless noted). All entries have ongoing free tiers, not just one-time trial credits.

> **Note:** Rate limits, model availability, and free tier terms change frequently. Always verify current limits on the provider's pricing page before building on a free tier. Last reviewed September 2026, with NVIDIA model IDs verified by live API call on 2026-09-20.

> **Free tiers kept shrinking through 2026.** Cerebras removed its open free tier entirely on August 17, OpenRouter holds unfunded accounts at 50 requests/day, and Google's consumer CLI free tier moved to a much smaller compute-based quota. Anything you read about these providers from early 2026 is too generous.

> **NVIDIA's catalog turned over hard in the last month.** Six of the nine chat models this guide recommended in August are now `410 Gone`, including GLM-5.2, MiniMax M3, Step 3.7 Flash, GPT-OSS 120B and Llama 3.3 70B. If you built a config from the August version of this file, most of it is dead. See [NVIDIA NIM](#nvidia-nim) for the replacements.

---

## Quick Reference

| Provider | Notable Free Models | OpenAI-Compatible | Credit Card Required |
|---|---|---|---|
| [NVIDIA NIM](#nvidia-nim) | GLM-5.3 and 5.3 Flash, Kimi K3, Nemotron 3, Gemma 4, Muse Glimmer, 80+ more | Yes | No (phone verification) |
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

Still the largest free catalog around, but it is smaller than it was. A live call to `GET /v1/models` on 2026-09-20 returned **82 models**, down from 103 a month earlier, spanning chat, vision, embedding, and reranking. The chat lineup is now led by GLM-5.3, Kimi K3, the Nemotron 3 family, Gemma 4 and Muse Glimmer. New models land fast, and old ones leave just as fast — plan on re-checking your model IDs monthly.

- **Sign-up:** Free NVIDIA Developer account. No credit card, but **phone verification is required** at signup.
- **Notable free models (each verified callable by live API call on 2026-09-20):**
  - `z-ai/glm-5.3` (MIT-licensed agentic coder, reasoning model, **the default recommendation here** — answered in ~4s on every attempt)
  - `z-ai/glm-5.3-flash` (smaller sibling; answers reliably but took ~55s per call in testing)
  - `moonshotai/kimi-k3` (2.8T params, 1M context, multimodal, strongest on paper; **now working again** after August's outage, but slow at 85–105s per call)
  - `nvidia/nemotron-3-super-120b-a12b` (120B / 12B active, 1M context)
  - `nvidia/nemotron-3-ultra-550b-a55b` (550B / 55B active, 262K context)
  - `nvidia/nemotron-3.5-lightning-30b-a3b` (small, fast, low-latency)
  - `meta/muse-glimmer-30b` (new this cycle; fastest agentic model tested here at ~2s)
  - `google/gemma-4-31b-it` (works, but slow and was previously gated — see the registration note below)
  - `openai/gpt-oss-20b` (the 120B version is **retired** as of 2026-09-03)
  - `deepseek-ai/deepseek-v4-flash-0731` (still listed and still answers, but **see the latency warning below** before making it a default)
- **OpenAI-compatible:** Yes — `https://integrate.api.nvidia.com/v1`
- **Key prefix:** `nvapi-`
- **Rate limits:** ~40 requests/min, shared across all models rather than per-model. You can apply for a 200 RPM increase through the NVIDIA Developer Forums.
- **Context range:** 4K to 1M tokens depending on model
- **Standout feature:** One API key, 80+ models. Largest free hosted catalog available anywhere.
- **Guides in this repo:** [Aider + NVIDIA NIM](Aider_Setup_NVIDIA_NIM.md) | [OpenCode + NVIDIA NIM](OpenCode_Setup_NVIDIA_NIM.md)

### Reading NVIDIA's error codes (the 403 vs 410 diagnostic)

NVIDIA's failure modes look similar from the outside but mean very different things. This table is the fastest way to tell them apart, and it is worth internalizing, because the wrong diagnosis sends you down a long dead end.

| Response | What it actually means | Fix |
|---|---|---|
| `410 Gone` with an end-of-life date | The model is genuinely retired. Note that this is returned **even when your key is invalid**, because the EOL check runs before authentication. | Pick a current model ID |
| `404 page not found` (bare) | The model slug does not exist. Usually a typo or a wrong org prefix, for example `zai-org/glm-5.3` instead of `z-ai/glm-5.3` | Check the URL slug on the model's page at build.nvidia.com |
| `404` with `Function '<uuid>': Not found for account` | The model exists, but **your account is not registered for it** | Open its page on build.nvidia.com and click **"Try API"** once, then retry |
| `403 Authorization failed` on **every** live model | Your key is invalid or **expired** | Generate a new key |
| `404` with an **empty body** and header `Nvcf-Status: errored` | The backing function itself is down. Not an account problem | Nothing you can do. Use another model and retry later |
| `503 Service temporarily overloaded` | Transient capacity shortage on that model's pool | Retry in a few seconds; it usually clears |
| `503 ResourceExhausted: Worker local total request limit reached (78/32)` | The model's **shared** worker pool is oversubscribed across all NVIDIA users, not a limit on your account | Retry later, or pick a less popular model. Raising your own RPM will not help |
| `429` after several fast calls | Your own per-account rate limit | Space requests out. Do not mistake this for a model failure |
| Request hangs with no response at all | **Two different causes.** On a *fresh* key it is the per-account registration gap failing silently. On a key that already worked, it is the model being slow enough to blow your client timeout | New key: click **"Try API"**. Otherwise raise your timeout before concluding it is broken |

> **The key-expiry trap:** NVIDIA keys carry a time-to-live that you select when you generate them, from one hour up to "never expires." One year is a common choice. Because a dead key returns `403` on live models while retired models still return their `410` notice, an expired key looks exactly like "every model I want got deprecated at once." If you see `403 Authorization failed` across several unrelated models, the problem is your key, not the catalog.

> **⚠️ The August recommendation is now a latency trap: `deepseek-ai/deepseek-v4-flash-0731`.** This was the headline pick in the previous version of this guide, chosen because it answered every request in under a second. On 2026-09-20 it is still listed and still returns `200` with correct output, but it has become drastically slower: four probes for a 16-token reply produced timeouts at 120s (twice), one success at **172s**, and a timeout at **300s**. There is no error code to catch here, which is what makes it nasty. An agent like Aider or OpenCode with a normal HTTP timeout will simply appear to hang, and you will go looking for a config bug that does not exist. Use `z-ai/glm-5.3` instead, which answered in about 4 seconds on every attempt.

> **Recently retired on NIM (each confirmed by a live `410` with its EOL date in the response body):** `meta/llama-3.3-70b-instruct` (EOL 2026-08-26), `stepfun-ai/step-3.7-flash` (2026-08-28), `openai/gpt-oss-120b` (2026-09-03), `minimaxai/minimax-m3` (2026-09-09), and `z-ai/glm-5.2` (2026-08-21, superseded by `z-ai/glm-5.3`). Earlier casualties: `deepseek-ai/deepseek-v4-flash` and `-v4-pro` (2026-08-07), `meta/llama-4-maverick-17b-128e-instruct` (2026-07-27), `qwen/qwen3-coder-480b-a35b-instruct` (2026-06-11), `moonshotai/kimi-k2-instruct` (2026-05-12). The `410` body names the exact retirement timestamp, which makes it the cheapest possible way to confirm a model is gone rather than merely misbehaving.

> **Expect to hit the registration gate.** This is not a rare edge case, and it is not limited to new keys. On 2026-09-20, `moonshotai/kimi-k2.6` and `nvidia/nemotron-nano-3-30b-a3b` both returned `Function '<uuid>': Not found for account` on a key that had been in use for a month and worked fine against a dozen other models. Both are listed in `/v1/models`. The catalog is not a reliable guide to what your key can actually call, so click "Try API" once on each model page you plan to use before you wire it into a config.

> **Good news on `moonshotai/kimi-k3`.** Throughout 2026-08-20 K3 returned `404` with an empty body and an `Nvcf-Status: errored` header, a backing-function failure that "Try API" could not fix. That outage is over: on 2026-09-20 it answered `200` on every attempt. It is slow, at 85 to 105 seconds for a short reply, so treat it as a model you reach for deliberately rather than a default. The wider lesson holds — a model can be listed, broken, and then quietly fixed, with no announcement either way. Re-probe before you trust a month-old note, including this one.

> **`503` is two different problems.** Plain `Service temporarily overloaded` is a brief capacity blip and usually clears on an immediate retry; `nvidia/nemotron-3-ultra-550b-a55b` threw one and answered fine 30 seconds later. But `ResourceExhausted: Worker local total request limit reached (78/32)`, seen on `poolside/laguna-xs-2.1`, is a different animal: those numbers are the **shared** worker pool across all NVIDIA users, and 78 queued against 32 slots means the model is simply oversubscribed. No amount of retrying or rate-limit tuning on your end fixes that one.

---

## Groq

**URL:** https://console.groq.com

Extremely fast inference on custom LPU hardware, still the lowest-latency free API available. The catalog is now much narrower than it used to be.

> **⚠️ This section was NOT re-verified in the September 2026 pass, and it is currently in dispute.** The model list below is the live-API result from 2026-08-20. As of 2026-09-20, Groq's own documentation at [console.groq.com/docs/models](https://console.groq.com/docs/models) lists **Llama 3.1 8B and Llama 3.3 70B as production models**, which directly contradicts the August finding that every Llama chat model had been removed. One of the two is wrong: either Groq restored them, or the docs page is stale. No Groq key was available to settle it during this review. **Send one throwaway request before trusting any Llama model ID here, in either direction.** Everything else in this section is unchanged from August and also unverified this cycle.

- **Sign-up:** Free account, no credit card
- **Catalog as of 2026-08-20 (13 models, verified by live API call **then**, not re-checked since):**
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

> **The Llama question, unresolved.** On 2026-08-20 a live catalog call returned 13 models with no Llama chat models among them: `llama-3.3-70b-versatile`, `llama-3.1-8b-instant` and the Llama 4 previews were all absent, leaving only the two `llama-prompt-guard-2` classifiers, which cannot hold a conversation. On 2026-09-20 Groq's docs list Llama 3.1 8B and Llama 3.3 70B as current production models. This guide cannot tell you which is right without a key. If you need a Llama model on Groq, test the ID directly; if you just need something that works, `openai/gpt-oss-20b` and `openai/gpt-oss-120b` were confirmed live in August and are documented as current now, so they are the safer bet either way.

> **Note the asymmetry with NVIDIA:** Groq's `/v1/models` does not report retirement dates the way NVIDIA's `410` responses do, so a missing model and a renamed model look identical from the outside. That is part of why the August reading was hard to pin down.

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
Free-tier context limits vary widely. DeepSeek V4 Flash 0731, Kimi K3, GLM-5.2, MiniMax M3, Nemotron 3 Super, and Gemini Flash all reach 1M. Step 3.7 Flash and Nemotron 3 Ultra sit around 256K. Groq caps at 131K.

**Check the daily cap, not just the per-minute one**
The requests-per-minute number is rarely what stops you. The daily ceiling is: OpenRouter at 50/day unfunded, Groq at 1,000/day on the GPT-OSS models, Google at 1,500/day, SambaNova at 20M tokens/day. Budget against that.

**Keep API keys out of code**
Store keys as environment variables or in config files outside your git repos. Never commit them. Many providers (NVIDIA, Groq, Gemini) will rotate or revoke keys that get leaked publicly. Note that OpenCode's `config.json` stores keys in plain text, so keep that file out of any repo. OpenCode also keeps a second, separate credential store at `~/.local/share/opencode/auth.json` for anything added via `opencode auth login`; `opencode auth list` shows what is in it.

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
