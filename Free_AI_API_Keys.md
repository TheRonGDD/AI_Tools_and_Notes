# Free AI API Keys

A reference guide to AI providers offering free API access, no credit card required (unless noted). All entries have ongoing free tiers, not just one-time trial credits.

> **Note:** Rate limits, model availability, and free tier terms change frequently. Always verify current limits on the provider's pricing page before building on a free tier. Last reviewed October 2026, with NVIDIA, Groq and OpenRouter model IDs verified by live API call on 2026-10-24.

> **Free tiers kept shrinking through 2026, and October claimed another one.** SambaNova's Free plan now requires a payment method before your first request, so it has moved to the [retired section](#sambanova) alongside Cerebras. Groq is down to 11 models, OpenRouter's zero-priced list dropped from 24 to 18 usable models, and both of September's slow-but-working NVIDIA picks (Kimi K3 and DeepSeek V4 Flash 0731) have been retired outright.

> **But it was not all loss.** NVIDIA added a genuinely fast new model, `minimaxai/minimax-m3.1-flash`, which answered in 0.2 to 0.7 seconds with correct tool calls, and its catalog grew from 82 to 85. Groq answered every chat request in under a third of a second. OpenRouter picked up three new free models, including NVIDIA's multimodal Nemotron 3.5 Super VL at a 1M context. Google added Gemini 3.8 Flash to its free tier.

---

## Quick Reference

| Provider | Notable Free Models | OpenAI-Compatible | Credit Card Required |
|---|---|---|---|
| [NVIDIA NIM](#nvidia-nim) | GLM-5.3, MiniMax M3.1 Flash, Nemotron 3 / 3.5, Muse Glimmer, 80+ more | Yes | No (phone verification) |
| [Groq](#groq) | GPT-OSS 120B/20B, Qwen3.8 27B | Yes | No |
| [Google AI Studio](#google-ai-studio) | Gemini 3.8 / 3.6 Flash, 3.5 / 3.1 Flash-Lite, Gemma 4 | Yes | No |
| [OpenRouter](#openrouter) | 18 `:free` models, including Nemotron 3.5 Super VL, Flint, Inkling | Yes | No |
| [Hugging Face](#hugging-face) | Thousands of models | Partial | No |
| [Cloudflare Workers AI](#cloudflare-workers-ai) | Llama, Mistral, image-gen, and more | Partial | No (Cloudflare account) |
| [Mistral AI](#mistral-ai) | Mistral Small and others | Yes | No |
| [Cohere](#cohere) | Command, Embed, Rerank | No (own SDK) | No |
| ~~SambaNova~~ | **[Payment method now required](#sambanova)** | Yes | **Yes, now required** |
| ~~Cerebras~~ | **[Free tier ended 2026-08-17](#cerebras)** | Yes | **Yes, now required** |

---

## NVIDIA NIM

**URL:** https://build.nvidia.com

Still the largest free catalog around. A live call to `GET /v1/models` on 2026-10-24 returned **85 models**, up from 82 in September, spanning chat, vision, embedding, and reranking. The turnover underneath that number is heavy: both of the slow-but-working models September's guide covered are gone, and several new ones arrived. New models land fast and old ones leave just as fast, so plan on re-checking your model IDs monthly.

- **Sign-up:** Free NVIDIA Developer account. No credit card, but **phone verification is required** at signup.
- **Notable free models (each sent a real completion on 2026-10-24, latency is what came back):**
  - `z-ai/glm-5.3` (MIT-licensed agentic coder, reasoning model, **still the default recommendation here**. Answered every probe, but at 7 to 27 seconds rather than September's ~4s)
  - `minimaxai/minimax-m3.1-flash` (**new this cycle, and the fastest capable model tested**: 0.2 to 0.7s on chat, 2.3s on a tool call, 1M context, text + image + video input)
  - `nvidia/nemotron-3-ultra-550b-a55b` (550B / 55B active, 262K context, 0.9 to 6.7s)
  - `nvidia/nemotron-3.5-lightning-30b-a3b` (small, fast, ~1–2s)
  - `nvidia/nemotron-3.5-super-vl-120b-a12b` (**new**: multimodal 120B / 12B active, 1M context. Fast when it answers, but one `500` and one 300s timeout in four calls)
  - `nvidia/nemotron-3.5-bolt-e4b` (**new**: small Gemma 4-based vision model, 0.3s)
  - `meta/muse-glimmer-30b` (1 to 9s, built for agent harnesses)
  - `nvidia/nemotron-3-nano-omni-30b-a3b-reasoning` (~1s, text + audio + image)
  - `openai/gpt-oss-20b` (~1–2s)
  - `stepfun-ai/step-5-preview` (**new**: 600B / 27B active preview, but 134s on one call and a timeout on another)
  - `deepseek-ai/deepseek-v4.1-flash` (**new**, and **a latency trap just like its predecessor**, see below)
- **OpenAI-compatible:** Yes — `https://integrate.api.nvidia.com/v1`
- **Key prefix:** `nvapi-`
- **Rate limits:** ~40 requests/min, shared across all models rather than per-model. You can apply for a 200 RPM increase through the NVIDIA Developer Forums.
- **Context range:** 4K to 1M tokens depending on model
- **Standout feature:** One API key, 80+ models. Largest free hosted catalog available anywhere.
- **Guides in this repo:** [Aider + NVIDIA NIM](Aider_Setup_NVIDIA_NIM.md) | [OpenCode + NVIDIA NIM](OpenCode_Setup_NVIDIA_NIM.md)

> **Every fast model above can actually drive an agent.** A model that answers "OK" quickly is not necessarily one that emits proper tool calls, so on 2026-10-24 each of MiniMax M3.1 Flash, Nemotron 3 Ultra, Nemotron 3.5 Lightning, Nemotron 3.5 Super VL, Nemotron 3.5 Bolt E4B, Muse Glimmer and GPT-OSS 20B was handed a `read_file` tool and asked to open a file. All seven returned `finish_reason: tool_calls` with correct arguments, in 0.6 to 3.2 seconds.

### Reading NVIDIA's error codes (the 403 vs 410 diagnostic)

NVIDIA's failure modes look similar from the outside but mean very different things. This table is the fastest way to tell them apart, and it is worth internalizing, because the wrong diagnosis sends you down a long dead end.

| Response | What it actually means | Fix |
|---|---|---|
| `410 Gone` with an end-of-life date | The model is genuinely retired. Note that this is returned **even when your key is invalid**, because the EOL check runs before authentication. | Pick a current model ID |
| `404 page not found` (bare) | The model slug does not exist. Usually a typo or a wrong org prefix, for example `zai-org/glm-5.3` instead of `z-ai/glm-5.3` | Check the URL slug on the model's page at build.nvidia.com |
| `404` with `Function '<uuid>': Not found for account` | The model exists, but **your account is not registered for it** | Open its page on build.nvidia.com and click **"Try API"** once, then retry |
| `403 Authorization failed` on **every** live model | Your key is invalid or **expired** | Generate a new key |
| `404` with an **empty body** and header `Nvcf-Status: errored` | The backing function itself is down. Not an account problem | Nothing you can do. Use another model and retry later |
| `500 Failed to generate completions` with `Nvcf-Status: errored` | A backend generation failure on that model. Seen once on `nemotron-3.5-super-vl` on 2026-10-24, which then answered in 3.4s | Retry; switch models if it repeats |
| `503 Service temporarily overloaded` | Transient capacity shortage on that model's pool | Retry in a few seconds; it usually clears |
| `503 ResourceExhausted: Worker local total request limit reached (108/32)` or `All workers are busy` | The model's **shared** worker pool is oversubscribed across all NVIDIA users, not a limit on your account | Retry later, or pick a less popular model. Raising your own RPM will not help |
| `429` after several fast calls | Your own per-account rate limit | Space requests out. Do not mistake this for a model failure |
| Request hangs with no response at all | **Two different causes.** On a *fresh* key it is the per-account registration gap failing silently. On a key that already worked, it is the model being slow enough to blow your client timeout | New key: click **"Try API"**. Otherwise raise your timeout before concluding it is broken |

> **The key-expiry trap:** NVIDIA keys carry a time-to-live that you select when you generate them, from one hour up to "never expires." One year is a common choice. Because a dead key returns `403` on live models while retired models still return their `410` notice, an expired key looks exactly like "every model I want got deprecated at once." If you see `403 Authorization failed` across several unrelated models, the problem is your key, not the catalog.

> **⚠️ `deepseek-ai/deepseek-v4.1-flash` repeats last month's trap.** September's guide warned that `deepseek-v4-flash-0731` still answered but took minutes. That model has since been retired (`410`, EOL 2026-09-21), and its replacement behaves the same way: on 2026-10-24, one call timed out at 180 seconds and the next succeeded after **166 seconds**, for a two-token reply. It returns a valid `200` with no error, so an agent simply appears to hang. Do not make it a default.

> **Retired since the September review (each confirmed by a live `410` with its EOL date in the response body on 2026-10-24):** `moonshotai/kimi-k3` (EOL **2026-10-15**) and `deepseek-ai/deepseek-v4-flash-0731` (EOL 2026-09-21). Kimi K3's model page now marks its free endpoint "Deprecated" while the paid partner endpoint stays up, so this looks like a decision rather than an outage. Earlier casualties: `minimaxai/minimax-m3` (2026-09-09), `openai/gpt-oss-120b` (2026-09-03), `stepfun-ai/step-3.7-flash` (2026-08-28), `meta/llama-3.3-70b-instruct` (2026-08-26), `z-ai/glm-5.2` (2026-08-21), `deepseek-ai/deepseek-v4-flash` and `-v4-pro` (2026-08-07), `meta/llama-4-maverick-17b-128e-instruct` (2026-07-27), `qwen/qwen3-coder-480b-a35b-instruct` (2026-06-11), `moonshotai/kimi-k2-instruct` (2026-05-12).

> **The registration gate now reaches models that already worked.** `nvidia/nemotron-3-super-120b-a12b` answered in about a second on 2026-09-20. On 2026-10-24, on the same key, it returned `Function '<uuid>': Not found for account` on all three attempts, as did `moonshotai/kimi-k2.6` and `nvidia/nemotron-nano-3-30b-a3b`. All three are still listed in `/v1/models`. So the catalog is not a reliable guide to what your key can call, and a model that worked last month can need a fresh "Try API" click this month. If one of your configured models suddenly 404s with that message, click "Try API" on its page before you assume it is gone.

> **GLM-5.3 got slower, not worse.** It answered all four probes on 2026-10-24 with correct output, at 27, 11, 7 and 10 seconds, against roughly 4 seconds a month ago. That is still usable as an agent default, and it is still the strongest reliable coder in the catalog, but if you want speed above all, `minimaxai/minimax-m3.1-flash` is now the better pick. `z-ai/glm-5.3-flash` took 136 seconds, so the "Flash" name is no guide here.

> **`503` is two different problems.** Plain `Service temporarily overloaded` is a brief capacity blip and usually clears on an immediate retry. `ResourceExhausted` is the **shared** worker pool across all NVIDIA users. On 2026-10-24 `poolside/laguna-xs-2.1` returned both `All workers are busy` and `Worker local total request limit reached (108/32)`, up from September's 78/32. No amount of retrying or rate-limit tuning on your end fixes that one.

---

## Groq

**URL:** https://console.groq.com

Extremely fast inference on custom LPU hardware, still the lowest-latency free API available. The catalog shrank from 13 models to **11** this cycle, but what remains is very fast: every chat model answered in 0.2 to 0.3 seconds on 2026-10-24.

- **Sign-up:** Free account, no credit card
- **Full catalog as of 2026-10-24 (11 models, verified by live API call), with context windows as the API reports them:**
  - `openai/gpt-oss-120b` (131,072, answered in 0.29s)
  - `openai/gpt-oss-20b` (131,072, 0.29s)
  - `openai/gpt-oss-safeguard-20b` (131,072)
  - `qwen/qwen3.8-27b` (131,072, 0.20s, the fastest response in this whole review)
  - `allam-2-7b` (4,096)
  - `canopylabs/orpheus-v1-english`, `canopylabs/orpheus-arabic-saudi` (4,000, text-to-speech)
  - `whisper-large-v3`, `whisper-large-v3-turbo` (448, speech-to-text)
  - `meta-llama/llama-prompt-guard-2-22m`, `meta-llama/llama-prompt-guard-2-86m` (512, safety classifiers, not chat models)
- **OpenAI-compatible:** Yes — `https://api.groq.com/openai/v1`
- **Key prefix:** `gsk_`
- **Rate limits (read from the response headers on 2026-10-24):** 1,000 requests/day and 8,000 tokens/min on the `openai/gpt-oss` models and on `qwen/qwen3.8-27b`. Whisper gets 2,000 audio requests/day.
- **Important:** limits are enforced **per organization, not per API key**. Extra keys under the same org share one bucket.
- **Standout feature:** Lowest latency of any free provider. Every listed model is available on the free tier, gated by rate limits only, with no credits system and no per-token charge.

> **`groq/compound` and `groq/compound-mini` are gone.** Groq's [deprecations page](https://console.groq.com/docs/deprecations) lists both as announced 2026-08-24 and shut down **2026-09-21**, the day after September's review recorded them as live. Both now return `404 model_not_found`. Groq names no replacement. They were Groq's agentic tool-using systems; the closest thing left is `openai/gpt-oss-120b` with your own tools.

> **Qwen 3.8 27B now reports the full 131,072 context on Groq,** up from the odd 131,042 it showed in September. The model ID is unchanged.

> **Groq's docs and API agree again.** September's review found the docs still listing Llama 3.1 8B and 3.3 70B as production models after the API had dropped them. The deprecations page now records both as shut down on 2026-08-16, with `openai/gpt-oss-20b` and `openai/gpt-oss-120b` as the replacements. A config pointing at `llama-3.3-70b-versatile` returns `404 model_not_found`.

> **Groq gives you less to diagnose with than NVIDIA does.** NVIDIA returns `410 Gone` with an exact retirement timestamp, so a dead model announces itself. Groq returns a generic `404 model_not_found`, which looks the same as a typo. Compare `/openai/v1/models` against your config directly rather than probing IDs one at a time, and check the deprecations page for dates.

> **Same model, opposite fates on two free providers.** `openai/gpt-oss-120b` reached end of life on NVIDIA NIM on 2026-09-03 and returns `410 Gone` there, while still answering in under a third of a second on Groq on 2026-10-24. A model being retired by one host says nothing about the others. This is a good argument for keeping a second provider configured.

> **Cheap upgrade:** adding a credit card with zero minimum spend unlocks up to 10x the free rate limits plus a 25% token discount. Worth knowing if 8K tokens/min is your only blocker.

---

## Google AI Studio

**URL:** https://aistudio.google.com

Google's developer portal for Gemini models.

- **Sign-up:** Google account, no credit card
- **Free models (per Google's pricing page, last updated 2026-10-22; not verified by live call, as this repo has no AI Studio key):**
  - `gemini-3.8-flash` (**new**, now the top free Flash model)
  - `gemini-3.6-flash`
  - `gemini-3.5-flash-lite`, `gemini-3.1-flash-lite` (1M context, fastest and cheapest)
  - `gemini-3-flash-preview` (marked "legacy")
  - `gemini-2.5-flash`, `gemini-2.5-flash-lite`
  - Gemma 4, plus TTS, live-audio, transcription and `gemini-embedding-2` models
- **OpenAI-compatible:** Yes — `https://generativelanguage.googleapis.com/v1beta/openai/`
- **Key prefix:** `AIza`
- **Rate limits:** Google's rate-limit page (updated 2026-10-15) no longer publishes free-tier numbers and says to view your active limits in AI Studio. Third-party reports for the newest Flash models range from about 20 to 1,500 requests/day, which is too wide to quote. Check your own project in AI Studio. RPD resets at midnight Pacific.
- **Quota is per project, not per key.** Creating a second API key in the same project does not double your allowance.
- **Standout feature:** 1M-token context on the free Flash models, and the free tier is permanent rather than trial-based

> **`gemini-3.7-flash` and `gemini-3.5-flash` are no longer on the pricing page.** August's guide listed both as free. The October page lists 3.8 Flash and 3.6 Flash, but neither 3.7 nor 3.5 Flash. Google has not published a shutdown notice for them that this review could find, so test yours before assuming either way. `gemini-2.5-flash-image` was formally shut down on 2026-10-02.

> **Pro models are paid (since April 1, 2026).** The Pro series, the new top-end **Gemini 4 Argon**, and the image-generation models are paid-only. Only Flash, Flash-Lite and Gemma remain free.

> **Privacy caveat:** Google may use free-tier inputs and outputs to improve its models. Commercial use is allowed, but if your prompts are sensitive, use the paid tier or Vertex AI, neither of which trains on your data.

> **Not the same as the CLI.** The Google AI Studio *API* free tier is alive and well. It was the consumer **Gemini CLI** login that shut down on June 18, 2026, see the [Antigravity CLI guide](Antigravity_CLI_Setup_Windows_11.md).

---

## OpenRouter

**URL:** https://openrouter.ai

A routing layer that aggregates many providers. Has a set of permanently free models tagged with `:free`.

- **Sign-up:** Free account, no credit card needed to sign up or to call `:free` models at a $0 balance
- **Zero-priced models as of 2026-10-24 (20 of 466 total, verified against the public `/api/v1/models` endpoint).** Two of those 20 are Google's Lyria music models, which show $0 token prices but bill $0.04 per clip, so they are not free. That leaves **18**:
  - `nvidia/nemotron-3.5-super-vl:free` (1,000,000, text + image + video, **new 2026-10-15**)
  - `recursive/flint-v1.0:free` (262,144, **new 2026-10-21**, a 40B / 2.5B-active coding model, also runnable locally under [Ollama](Ollama_Local_AI_Windows_11.md))
  - `xingchen-agi/xing4.0-29b-a4b:free` (262,144, **new 2026-10-16**, China Telecom's successor to TeleChat)
  - `nvidia/nemotron-3-ultra-550b-a55b:free` and `nvidia/nemotron-3.5-lightning:free` (1,000,000 each)
  - `nvidia/nemotron-3-super-120b-a12b:free` (262,144), `nvidia/nemotron-3-nano-omni-30b-a3b-reasoning:free` (256,000)
  - `nvidia/nemotron-3.5-content-safety:free` (128,000, a classifier, not a chat model)
  - `thinkingmachines/inkling:free` and `inkling-small:free` (1,048,576 each)
  - `google/gemma-4-31b-it:free` and `gemma-4-26b-a4b-it:free` (262,144 each)
  - `poolside/laguna-s-2.1:free` and `laguna-xs-2.1:free` (262,144 each)
  - `dots-studio/dots-3-note-preview:free` (512,000), `cohere/north-mini-code:free` (256,000)
  - `liquid/lfm-2.5-2.6b:free` (65,536)
  - `openrouter/free` (200,000, OpenRouter's own auto-router across free endpoints)
- **OpenAI-compatible:** Yes — `https://openrouter.ai/api/v1`
- **Key prefix:** `sk-or-`
- **Rate limits:** 20 requests/min always, plus **50 requests/day on an unfunded account**
- **Standout feature:** A single API key works across dozens of models and providers, which makes it the easiest way to test many models without juggling keys

> **Seven free models from September are gone, including the two this repo singled out.** `qwen/qwen3.8-27b:free` and `z-ai/glm-5.2:free` both lost their free endpoints; the paid versions remain. Also gone: `nex-agi/nex-n2.5-pro:free`, `nex-n2.5-mini:free`, and all three `inclusionai/ling-3.0-*:free` models. If you need Qwen 3.8 27B free, Groq still has it, at 131,072 context and 0.2 seconds per call.

> **The $10 lever:** buying $10 in credits **once** raises the daily cap from 50 to 1,000 requests/day permanently, and the credits never expire. This is still the single best value unlock among the providers in this guide. (Minimum purchase is $5, but $10 is the threshold that moves the daily limit.) The 20 RPM cap does not change; only the daily ceiling moves.

> Guides quoting "200 requests/day" for OpenRouter's free tier are out of date. The current tiers are 50/day and 1,000/day.

> **OpenRouter's catalog is checkable without a key.** `GET https://openrouter.ai/api/v1/models` is public and unauthenticated. It returns every model with its pricing and context length, so a model whose `pricing.prompt` and `pricing.completion` are both `"0"` is genuinely free right now. Read the description too: this month's Lyria entries show zero token prices and still charge per clip.

> **⚠️ "Stealth models" on OpenRouter are temporary promotions, not a free tier.** OpenRouter periodically hosts unreleased models under anonymous aliases, free during a short preview while the vendor collects usage data. These are genuinely capable and genuinely free *while they last*, but they do not qualify under this guide's ongoing-free-tier rule, and they disappear without notice. See [Union Alpha](#union-alpha-stealth-model-already-over) in the retired section for how fast that can happen.

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

> Cloudflare also released **Clef** and **Clef Flash** in September 2026, open-source "decision models" that return choices and probabilities rather than text. They run locally under [Ollama](Ollama_Local_AI_Windows_11.md) 0.35.1 and newer.

---

## Mistral AI

**URL:** https://console.mistral.ai

Mistral's own API platform. Offers a free experimentation tier. *(Not re-verified in the October pass.)*

- **Sign-up:** Free account, no credit card for free tier (phone verification required)
- **Notable free models:** Mistral Small, Codestral, open-weight models
- **OpenAI-compatible:** Yes — `https://api.mistral.ai/v1`
- **Rate limits:** 1 request/sec, 500K tokens/min, 1B tokens/month on the Experimentation tier
- **Docs:** https://docs.mistral.ai

---

## Cohere

**URL:** https://dashboard.cohere.com

Specializes in text generation, embeddings, and retrieval. Good free trial keys for non-commercial use. *(Not re-verified in the October pass.)*

- **Sign-up:** Free account, no credit card
- **Notable free models:** Command, Command R+, Embed, Rerank
- **OpenAI-compatible:** No, uses Cohere's own SDK (`pip install cohere`)
- **Rate limits:** 20 requests/min, 1,000 requests/month on a trial key
- **Standout feature:** Best-in-class embeddings and reranking models on the free tier
- **Docs:** https://docs.cohere.com

---

## Retired: No Longer Free

These providers were listed in earlier versions of this guide and no longer meet its criteria.

### SambaNova

**Status:** Payment method required. Removed from the free list in the October 2026 review.

SambaNova was listed here through September as a no-card Developer tier with MiniMax M2.7, DeepSeek V3.1, Llama 3.3 70B and GPT-OSS 120B. Its [plans page](https://cloud.sambanova.ai/plans) now says of the Free plan: *"Add a payment method and purchase credits to run your first requests."* A third-party pricing tracker dates the change to around **2026-08-14**, which means the August and September versions of this guide were already wrong about it. They were marked as not re-checked, and that is exactly the gap it fell through.

Its October free-tier table also reportedly narrowed to three production models (Llama 3.3 70B, DeepSeek V3.1, GPT-OSS 120B) plus two previews, with MiniMax M2.7 moved to the paid Developer tier. That is moot if you cannot make a request without a card.

If you were using SambaNova for large open models, the nearest free substitutes are NVIDIA NIM (`z-ai/glm-5.3`, `nvidia/nemotron-3-ultra-550b-a55b`) and Groq (`openai/gpt-oss-120b`).

### Cerebras

**Status:** Free tier ended **2026-08-17**.

Cerebras had one of the better deals here: 1M tokens/day at 2,600+ tokens/sec on wafer-scale hardware. That shrank on 2026-05-31 (catalog cut from about a dozen models to two, throughput cut to 5 RPM / 30K TPM), and then ended outright. As of July 21, 2026 new signups got a Free Trial instead of a free tier, and on August 17 existing accounts were migrated onto it.

What replaced it: **$5 in one-time credits, granted only after you add a verified payment method, expiring 30 days after issue.** There is no renewing allowance and no always-free per-model quota. `zai-glm-4.7`, one of the two remaining free models, was also scheduled for deprecation on the same date.

If you were using Cerebras for speed, the nearest free substitute is Groq, which answered every chat request in this review in 0.2 to 0.3 seconds.

### xAI / Grok

**Status:** Never had an ongoing free tier, so it does not qualify.

New accounts receive $25 in promotional credits that expire after 30 days, then it is pay-per-token. The $150/month data-sharing credit program is no longer reliably available to new developers. xAI also retired eight models in May 2026, including `grok-3`, `grok-4-fast`, `grok-4-1-fast-reasoning`, and `grok-code-fast-1`; configs pointing at those will fail, and the migration target (`grok-4.3`) costs roughly 6x more per input token than the budget models it replaced.

xAI's August 2026 [Grok Bot](https://x.ai/news/introducing-grok-bot) release is a paid agent product ($300/mo SuperGrok Heavy, $200/mo Cursor Ultra, or $120/seat/mo Cursor Teams Premium), closed source, and desktop and iOS rather than CLI, so it is out of scope for this repo on every axis.

---

### Union Alpha (stealth model, already over)

**Status:** Was free for a preview period measured in days, not an ongoing free tier. **The alias is dead.**

Union Alpha appeared on OpenRouter around 2026-09-16 as an anonymous "stealth" model, promoted by OpenCode as free for the following week, with no training on your data, built for agentic coding, and accepting images. It was genuinely strong and it got heavy use.

By 2026-09-20, `stealth/union-alpha` returned **`404` from OpenRouter's model API**. The model was revealed as **Pareto**, from Unbiased, and is **paid**. As of 2026-10-24 there are two dated versions: `unbiased/pareto-26.9` at $2.50 per million input tokens (262,144 context) and a newer `unbiased/pareto-26.10` at $0.80 input / $3.20 output with a 1,048,576 context.

So the free window lasted a few days rather than the advertised week, which is the whole point of listing it here. **A stealth-model promotion is a vendor buying evaluation data, not a free tier.** If you build a config around one, you are writing code with a deadline you were never told. Enjoy them while they run, but keep a real free provider configured underneath, and check `GET /api/v1/models` before assuming one is still live, because that call is public and takes a second.

---

## Tips for Using Free APIs

**Verify model IDs before you trust a guide, including this one**
Free hosted models churn fast enough that any written guide is partly stale the week it is published. The cheapest check is a one-token request against the endpoint. A `410` tells you the model is retired and usually names the EOL date; a `404` tells you the slug is wrong. Both take a second to test and save a long debugging session.

**Time your calls, not just their status codes**
Two months running, an NVIDIA model has passed every error check and still been unusable: `deepseek-v4-flash-0731` in September, `deepseek-v4.1-flash` in October, each taking around three minutes per reply. An agent with a normal timeout presents that as its own hang.

**Re-check providers you have stopped checking**
SambaNova's no-card path ended around August 14 and this guide carried it for two more months under a "not re-checked" label. A provider you are not actively verifying can be wrong for longer than you think.

**Rotate providers to avoid rate limits**
Most tools (Aider, OpenCode, and similar) let you switch models mid-session. If you hit a rate limit on one provider, switch to another. Groq is best for speed, NVIDIA NIM for variety and context length, OpenRouter for trying many models through one key.

**Prefer OpenAI-compatible providers**
Tools like Aider, OpenCode, and Codex work out of the box with any OpenAI-compatible endpoint. Set `OPENAI_BASE_URL` and `OPENAI_API_KEY` and you are done.

**Watch for context window differences**
Free-tier context limits vary widely. GLM-5.3, MiniMax M3.1 Flash, Nemotron 3.5 Super VL, Nemotron 3.5 Lightning and Gemini Flash all reach 1M. Nemotron 3 Ultra sits at 262K on NIM (1M on OpenRouter's free endpoint). Groq caps at 131K.

**Check the daily cap, not just the per-minute one**
The requests-per-minute number is rarely what stops you. The daily ceiling is: OpenRouter at 50/day unfunded, Groq at 1,000/day on the GPT-OSS and Qwen models, Google at whatever AI Studio shows for your project. Budget against that.

**Keep API keys out of code**
Store keys as environment variables or in config files outside your git repos. Never commit them. Many providers (NVIDIA, Groq, Gemini) will rotate or revoke keys that get leaked publicly. Note that OpenCode's config file stores keys in plain text, so keep that file out of any repo. OpenCode 1.x also keeps a second, separate credential store at `~/.local/share/opencode/auth.json` for anything added via `opencode auth login`; `opencode auth list` shows what is in it.

**Track your key expiry dates**
NVIDIA keys now expire on a schedule you choose at generation time. Set a calendar reminder, or pick "never expires" if the account is only used for personal projects. An expired key produces a `403` that reads like a model problem rather than an auth problem.

**Assume free tiers shrink**
Every provider in this guide that changed its free tier during 2026 made it smaller, not larger, and three of them (Cerebras, SambaNova, Groq) did so with little warning. If a free tier is load-bearing for something you care about, have a second provider configured before you need it.

---

## Datacenter Location vs. Model Origin

All providers listed in this guide operate US or EU-based datacenters, so your prompts and data are processed on Western infrastructure. However, several of the strongest open-weight models were created by Chinese organizations:

| Model | Made by | Served via (in this guide) | Data goes to |
|---|---|---|---|
| GLM-5.3 | Zhipu AI / Z.ai (China) | NVIDIA NIM (US) | US |
| MiniMax M3.1 Flash | MiniMax (China) | NVIDIA NIM (US) | US |
| DeepSeek V4.1 Flash | DeepSeek (China) | NVIDIA NIM (US) | US |
| Step 5 Preview | StepFun (China) | NVIDIA NIM (US) | US |
| Kimi K2.6 | Moonshot AI (China) | NVIDIA NIM (US) | US |
| Qwen 3.8 / 3.6 / 3.5 | Alibaba (China) | Groq (US) or Ollama (local) | Local or US |
| Xing4.0 | TeleAI / China Telecom (China) | OpenRouter | Depends on the upstream provider OpenRouter routes to |

The model weights being of Chinese origin does not mean your data leaves US/EU infrastructure when using the providers above. The distinction matters if you are working with sensitive or proprietary code and your compliance team has views on either model lineage or data location. OpenRouter is a router, so for any model there, check which upstream provider served the request.

**One exception to be aware of:** DeepSeek offers their own direct API at `api.deepseek.com`. That endpoint is hosted in China, so data sent there goes to Chinese servers. The guides in this repo do not use that endpoint, but it is worth knowing if you encounter it elsewhere.
