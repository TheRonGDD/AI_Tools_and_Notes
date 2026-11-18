# Free AI API Keys

A reference guide to AI providers offering free API access, no credit card required (unless noted). All entries have ongoing free tiers, not just one-time trial credits.

> **Note:** Rate limits, model availability, and free tier terms change frequently. Always verify current limits on the provider's pricing page before building on a free tier. Last reviewed November 2026. NVIDIA and OpenRouter model IDs verified by live API call on 2026-11-18; Groq and Google checked against their own documentation only this cycle (no key was available for a live call).

> **November's loss was NVIDIA's catalog.** It fell from 85 models to **32**, and the model every guide in this repo recommended, `z-ai/glm-5.3`, reached end of life on **2026-10-28**, four days after October's review re-verified it. GPT-OSS 20B, DeepSeek V4.1 Flash and Kimi K2.6 followed in mid-November. OpenRouter's usable free list dropped from 18 to 15.

> **What arrived:** Mistral Large 4 and Kimi K3.1 Pro on NVIDIA NIM, Gemini 4 Hydrogen on Google's free tier (docs only), and new free models on OpenRouter (ERNIE 5.5 Flash Preview, Ling 3.2 Flash, Particle-1, LFM 3 1.7B). Nemotron 3 Ultra, now the default in this repo, answered every call in under 2 seconds and made a correct tool call in 1.0s.

---

## Quick Reference

| Provider | Notable Free Models | OpenAI-Compatible | Credit Card Required |
|---|---|---|---|
| [NVIDIA NIM](#nvidia-nim) | Nemotron 3 Ultra / 3.5, Mistral Large 4, Gemma 4, Muse Glimmer, ~30 total | Yes | No (phone verification) |
| [Groq](#groq) | GPT-OSS 120B/20B, Qwen3.8 27B | Yes | No |
| [Google AI Studio](#google-ai-studio) | Gemini 4 Hydrogen, 3.8 / 3.6 Flash, 3.5 / 3.1 Flash-Lite, Gemma 4 | Yes | No |
| [OpenRouter](#openrouter) | 15 `:free` models, including Nemotron 3 Ultra, Flint, ERNIE 5.5 Flash Preview | Yes | No |
| [Hugging Face](#hugging-face) | Thousands of models | Partial | No |
| [Cloudflare Workers AI](#cloudflare-workers-ai) | Llama, Mistral, image-gen, and more | Partial | No (Cloudflare account) |
| [Mistral AI](#mistral-ai) | Mistral Small and others | Yes | No |
| [Cohere](#cohere) | Command, Embed, Rerank | No (own SDK) | No |
| ~~SambaNova~~ | **[Payment method now required](#sambanova)** | Yes | **Yes, now required** |
| ~~Cerebras~~ | **[Free tier ended 2026-08-17](#cerebras)** | Yes | **Yes, now required** |

---

## NVIDIA NIM

**URL:** https://build.nvidia.com

Still the best free catalog for agent work, but much smaller than it was. A live call to `GET /v1/models` on 2026-11-18 returned **32 models**, down from 85 on 2026-10-24, and that count includes embedding, safety, translation and video-detection models; about 15 are general chat models. The default this repo recommended for three months, `z-ai/glm-5.3`, is among the losses. New models land fast and old ones leave just as fast, so plan on re-checking your model IDs monthly.

- **Sign-up:** Free NVIDIA Developer account. No credit card, but **phone verification is required** at signup.
- **Notable free models (each sent a real completion and a `read_file` tool call on 2026-11-18; latency is what came back):**
  - `nvidia/nemotron-3-ultra-550b-a55b` (550B / 55B active, 262K context. **The default recommendation here from November.** 0.8 to 1.9s on chat, 1.0s on a correct tool call)
  - `mistralai/mistral-large-4-1t-a52b` (**new**: Mistral Large 4, 1T / 52B active, text + image. 1.8 to 10s on chat, 2.8 to 4.8s on a tool call. OpenRouter lists 1M context for the same model; NVIDIA's figure not confirmed)
  - `nvidia/nemotron-3.5-lightning-30b-a3b` (small and fast: 1.3 to 1.9s chat, 0.5 to 0.8s tool call)
  - `nvidia/nemotron-3.5-super-vl-120b-a12b` (multimodal 120B / 12B active, 1M context, 0.6 to 0.9s. October's `500` and timeout did not recur)
  - `nvidia/nemotron-3.5-bolt-e4b` (small Gemma 4-based vision model, 0.2 to 0.5s)
  - `nvidia/nemotron-3-nano-omni-30b-a3b-reasoning` (text + audio + image, 0.8 to 1.7s)
  - `google/gemma-4-31b-it` (0.6s chat, 1.6s tool call)
  - `poolside/laguna-xs-2.1` (0.2 to 0.7s)
  - `meta/muse-glimmer-30b` (8 to 13s, built for agent harnesses)
  - `stepfun-ai/step-5-preview` (600B / 27B active preview. 1.1 to 17.5s on chat, 36s on a tool call. Much better than October's 134s, still uneven)
  - `minimaxai/minimax-m3.1-flash` (still correct, but **48 to 75 seconds** per reply and 64s for a tool call, against 0.2 to 0.7s in October. **No longer the fast pick**)
  - `moonshotai/kimi-k3.1-pro` (**new**: successor to the retired Kimi K3, 1M context per OpenRouter's listing. 119s on chat, 30s on a tool call. Works, but too slow for a default)
  - `z-ai/glm-5.3-flash` (timed out at 200s and 300s. The only GLM left on NIM, and not usable)
- **OpenAI-compatible:** Yes — `https://integrate.api.nvidia.com/v1`
- **Key prefix:** `nvapi-`
- **Rate limits:** ~40 requests/min, shared across all models rather than per-model. You can apply for a 200 RPM increase through the NVIDIA Developer Forums.
- **Context range:** 4K to 1M tokens depending on model
- **Standout feature:** One API key, about 30 models including several frontier-class agents. Still the strongest free option for agent work.
- **Guides in this repo:** [Aider + NVIDIA NIM](Aider_Setup_NVIDIA_NIM.md) | [OpenCode + NVIDIA NIM](OpenCode_Setup_NVIDIA_NIM.md)

> **Most fast models above can actually drive an agent.** A model that answers "OK" quickly is not necessarily one that emits proper tool calls, so on 2026-11-18 each of Nemotron 3 Ultra, Mistral Large 4, Nemotron 3.5 Lightning, Super VL and Bolt E4B, Nemotron 3 Nano Omni, Gemma 4 31B, Laguna XS 2.1 and Muse Glimmer was handed a `read_file` tool and asked to open a file. All nine returned `finish_reason: tool_calls` with correct arguments, in 0.5 to 8.3 seconds (0.5 to 4.8s without Glimmer). Give reasoning models room: with `max_tokens` of 64 or less, Mistral Large 4 ran out of tokens before calling the tool.

### Reading NVIDIA's error codes (the 403 vs 410 diagnostic)

NVIDIA's failure modes look similar from the outside but mean very different things. This table is the fastest way to tell them apart, and it is worth internalizing, because the wrong diagnosis sends you down a long dead end.

| Response | What it actually means | Fix |
|---|---|---|
| `410 Gone` with an end-of-life date | The model is genuinely retired. Note that this is returned **even when your key is invalid**, because the EOL check runs before authentication. | Pick a current model ID |
| `404 page not found` (bare) | The model slug does not exist. Usually a typo or a wrong org prefix, for example `nemotron/...` instead of `nvidia/...` | Check the URL slug on the model's page at build.nvidia.com |
| `404` with `Function '<uuid>': Not found for account` | Usually the model exists but **your account is not registered for it**. Sometimes the model was **recently retired** and NVIDIA has not switched it to `410` yet (see below) | Open its page on build.nvidia.com and click **"Try API"** once, then retry. If that does not help, treat it as retired |
| `403 Authorization failed` on **every** live model | Your key is invalid or **expired** | Generate a new key |
| `404` with an **empty body** and header `Nvcf-Status: errored` | The backing function itself is down. Not an account problem | Nothing you can do. Use another model and retry later |
| `500 Failed to generate completions` with `Nvcf-Status: errored` | A backend generation failure on that model. Seen once on `nemotron-3.5-super-vl` on 2026-10-24 (not seen in November) | Retry; switch models if it repeats |
| `503 Service temporarily overloaded` | Transient capacity shortage on that model's pool | Retry in a few seconds; it usually clears |
| `503 ResourceExhausted: Worker local total request limit reached (108/32)` or `All workers are busy` | The model's **shared** worker pool is oversubscribed across all NVIDIA users, not a limit on your account | Retry later, or pick a less popular model. Raising your own RPM will not help |
| `429` after several fast calls | Your own per-account rate limit | Space requests out. Do not mistake this for a model failure |
| Request hangs with no response at all | **Two different causes.** On a *fresh* key it is the per-account registration gap failing silently. On a key that already worked, it is the model being slow enough to blow your client timeout | New key: click **"Try API"**. Otherwise raise your timeout before concluding it is broken |

> **The key-expiry trap:** NVIDIA keys carry a time-to-live that you select when you generate them, from one hour up to "never expires." One year is a common choice. Because a dead key returns `403` on live models while retired models still return their `410` notice, an expired key looks exactly like "every model I want got deprecated at once." If you see `403 Authorization failed` across several unrelated models, the problem is your key, not the catalog.

> **Retired since the October review (each confirmed by a live `410` with its EOL date in the response body on 2026-11-18):** `z-ai/glm-5.3` (EOL **2026-10-28**), `openai/gpt-oss-20b` (**2026-11-18**), `deepseek-ai/deepseek-v4.1-flash` (2026-11-16; October flagged it as a 166-second latency trap) and `moonshotai/kimi-k2.6` (2026-11-15). Two models October described as needing a "Try API" click are also retired, with EOL dates that predate October's test: `nvidia/nemotron-3-super-120b-a12b` (2026-10-03) and `nvidia/nemotron-nano-3-30b-a3b` (2026-09-01). Earlier casualties: `moonshotai/kimi-k3` (2026-10-15), `deepseek-ai/deepseek-v4-flash-0731` (2026-09-21), `minimaxai/minimax-m3` (2026-09-09), `openai/gpt-oss-120b` (2026-09-03), `stepfun-ai/step-3.7-flash` (2026-08-28), `meta/llama-3.3-70b-instruct` (2026-08-26), `z-ai/glm-5.2` (2026-08-21), `deepseek-ai/deepseek-v4-flash` and `-v4-pro` (2026-08-07), `meta/llama-4-maverick-17b-128e-instruct` (2026-07-27), `qwen/qwen3-coder-480b-a35b-instruct` (2026-06-11), `moonshotai/kimi-k2-instruct` (2026-05-12).

> **Correction to October: `Not found for account` can also mean "already retired".** October's review reported that `nvidia/nemotron-3-super-120b-a12b`, `moonshotai/kimi-k2.6` and `nvidia/nemotron-nano-3-30b-a3b` returned `Function '<uuid>': Not found for account` on 2026-10-24, and read that as the registration gate catching models that used to work. On 2026-11-18 all three return `410 Gone`, and two of them carry end-of-life dates **before** October's test: Nemotron 3 Super on 2026-10-03 and Nemotron Nano 3 on 2026-09-01. So for a while after retirement, NVIDIA can answer a dead model with the account-gate message instead of `410`. This is an inference from the dates, not something NVIDIA documents. If "Try API" does not fix a `Not found for account`, assume the model is gone and check again in a week.

> **GLM-5.3 is gone.** It was the default here from September to October and retired on 2026-10-28. For a strong reasoning-and-coding default, use `nvidia/nemotron-3-ultra-550b-a55b` (fast) or `mistralai/mistral-large-4-1t-a52b`.

> **`503` is two different problems.** Plain `Service temporarily overloaded` is a brief capacity blip and usually clears on an immediate retry. `ResourceExhausted` is the **shared** worker pool across all NVIDIA users. On 2026-10-24 `poolside/laguna-xs-2.1` returned both `All workers are busy` and `Worker local total request limit reached (108/32)`, up from September's 78/32 (no `503` of either kind appeared in about 40 calls on 2026-11-18, and Laguna answered in 0.2s). No amount of retrying or rate-limit tuning on your end fixes that one.

---

## Groq

**URL:** https://console.groq.com

Extremely fast inference on custom LPU hardware, still the lowest-latency free API available. Every chat model answered in 0.2 to 0.3 seconds on 2026-10-24. *(Not re-checked live in November: no key was available. The November notes below come from Groq's own docs on 2026-11-18.)*

- **Sign-up:** Free account, no credit card
- **Full catalog as of 2026-10-24 (11 models, verified by live API call), with context windows as the API reports them:**
  - `openai/gpt-oss-120b` (131,072, answered in 0.29s)
  - `openai/gpt-oss-20b` (131,072, 0.29s)
  - `openai/gpt-oss-safeguard-20b` (131,072)
  - `qwen/qwen3.8-27b` (131,072, 0.20s, the fastest response in this whole review)
  - `allam-2-7b` (4,096; **no longer listed in Groq's docs as of 2026-11-18**, with no deprecation notice. Not verified against the API)
  - `canopylabs/orpheus-v1-english`, `canopylabs/orpheus-arabic-saudi` (4,000, text-to-speech)
  - `whisper-large-v3`, `whisper-large-v3-turbo` (448, speech-to-text)
  - `meta-llama/llama-prompt-guard-2-22m`, `meta-llama/llama-prompt-guard-2-86m` (512, safety classifiers, not chat models)
- **OpenAI-compatible:** Yes — `https://api.groq.com/openai/v1`
- **Key prefix:** `gsk_`
- **Rate limits (read from the response headers on 2026-10-24):** 1,000 requests/day, 8,000 tokens/min and 200,000 tokens/day on the `openai/gpt-oss` models and on `qwen/qwen3.8-27b`. Whisper gets 2,000 audio requests/day. Groq's docs still listed the same free-plan numbers on 2026-11-18, and no deprecation has been announced since 2026-09-21.
- **Important:** limits are enforced **per organization, not per API key**. Extra keys under the same org share one bucket.
- **Standout feature:** Lowest latency of any free provider. Every listed model is available on the free tier, gated by rate limits only, with no credits system and no per-token charge.

> **`groq/compound` and `groq/compound-mini` are gone.** Groq's [deprecations page](https://console.groq.com/docs/deprecations) lists both as announced 2026-08-24 and shut down **2026-09-21**, the day after September's review recorded them as live. Both now return `404 model_not_found`. Groq names no replacement. They were Groq's agentic tool-using systems; the closest thing left is `openai/gpt-oss-120b` with your own tools.

> **Qwen 3.8 27B now reports the full 131,072 context on Groq,** up from the odd 131,042 it showed in September. The model ID is unchanged.

> **Groq's docs and API agree again.** September's review found the docs still listing Llama 3.1 8B and 3.3 70B as production models after the API had dropped them. The deprecations page now records both as shut down on 2026-08-16, with `openai/gpt-oss-20b` and `openai/gpt-oss-120b` as the replacements. A config pointing at `llama-3.3-70b-versatile` returns `404 model_not_found`.

> **Groq gives you less to diagnose with than NVIDIA does.** NVIDIA returns `410 Gone` with an exact retirement timestamp, so a dead model announces itself. Groq returns a generic `404 model_not_found`, which looks the same as a typo. Compare `/openai/v1/models` against your config directly rather than probing IDs one at a time, and check the deprecations page for dates.

> **Same model, opposite fates on two free providers.** `openai/gpt-oss-120b` reached end of life on NVIDIA NIM on 2026-09-03 and returns `410 Gone` there, while still answering in under a third of a second on Groq on 2026-10-24. A model being retired by one host says nothing about the others. This is a good argument for keeping a second provider configured.

> **⚠️ Groq's free tier cannot drive OpenCode.** The per-minute token cap applies to a single request, and OpenCode's system prompt plus tool definitions is bigger than the cap before you type anything. On 2026-10-24, OpenCode 2.1.4 got `Request too large ... Limit 8000, Requested 8307` on `openai/gpt-oss-120b` and `Limit 7000, Requested 8718` (an input-tokens-per-minute limit) on `qwen/qwen3.8-27b`. Groq is still excellent for scripts, short chats and lighter tools, but use NVIDIA NIM for agent work. Separately, Groq caps `qwen/qwen3.8-27b` output at **16,384** tokens, so a config with a higher output limit fails with a `max_completion_tokens` error before it even reaches the TPM check.

> **Cheap upgrade:** adding a credit card with zero minimum spend unlocks up to 10x the free rate limits plus a 25% token discount. Worth knowing if 8K tokens/min is your only blocker, and the only way to run an agent like OpenCode against Groq.

---

## Google AI Studio

**URL:** https://aistudio.google.com

Google's developer portal for Gemini models.

- **Sign-up:** Google account, no credit card
- **Free models (per Google's [pricing](https://ai.google.dev/gemini-api/docs/pricing) and [deprecations](https://ai.google.dev/gemini-api/docs/deprecations) pages, read 2026-11-18; not called live this month, no key was available):**
  - `gemini-4-hydrogen` (**new, 2026-11-12**: the first free Gemini 4 model, 1M context, aimed at "everyday coding and agentic workflows")
  - `gemini-3.8-flash` (47s on one call and a `503` "high demand" on the next, on 2026-10-24)
  - `gemini-3.6-flash` (1.3s on 2026-10-24, the fast pick)
  - `gemini-3.5-flash-lite`, `gemini-3.1-flash-lite` (1M context, fastest and cheapest. 3.1 Flash-Lite has a shutdown date of **2027-05-07**)
  - `gemini-3-flash-preview` (marked "legacy")
  - `gemini-2.5-flash-lite`
  - Gemma 4, plus TTS, live-audio, transcription and `gemini-embedding-2` models
- **OpenAI-compatible:** Yes — `https://generativelanguage.googleapis.com/v1beta/openai/`
- **Key prefix:** `AIza`
- **Rate limits:** Google's rate-limit page (updated 2026-10-15) no longer publishes free-tier numbers and says to view your active limits in AI Studio. Third-party reports for the newest Flash models range from about 20 to 1,500 requests/day, which is too wide to quote. Check your own project in AI Studio. RPD resets at midnight Pacific. Google's usage-tier table also says some new projects must complete a verification step before the free tier works (docs, 2026-11-18).
- **Quota is per project, not per key.** Creating a second API key in the same project does not double your allowance.
- **Standout feature:** 1M-token context on the free Flash models, and the free tier is permanent rather than trial-based

> **`gemini-3.7-flash` and `gemini-3.5-flash` are aliases now, not dying models.** October found both off the pricing page but still answering. Google's deprecations page now lists their shutdown date as "N/A" and says each endpoint "is not shutting down and automatically routes API requests" to `gemini-3.8-flash` and `gemini-3.6-flash` respectively. That probably explains why 3.7 Flash answered at about 50 seconds in October, the same speed as 3.8 Flash. Old configs keep working, but you are really getting the newer model.

> **`gemini-2.5-pro` and `gemini-2.5-flash` are deprecated and re-route on 2026-12-01.** From that date, requests to `gemini-2.5-pro` go to `gemini-3.8-flash` and requests to `gemini-2.5-flash` go to `gemini-3.5-flash-lite`. `gemini-2.5-flash-image` was shut down on 2026-10-02.

> **Pro and top-end models are paid (since April 1, 2026).** Gemini 4 Carbon (the new top model, 2026-11-10), Gemini 4 Argon, the Pro series and the image-generation models are all "Not available" on the free tier. **Gemini 4 Hydrogen is the exception.** Note that Carbon's paid price doubles on 2026-12-01, so an accidental paid key costs more next month.

> **Privacy caveat:** Google may use free-tier inputs and outputs to improve its models. Commercial use is allowed, but if your prompts are sensitive, use the paid tier or Vertex AI, neither of which trains on your data.

> **Not the same as the CLI.** The Google AI Studio *API* free tier is alive and well. It was the consumer **Gemini CLI** login that shut down on June 18, 2026, see the [Antigravity CLI guide](Antigravity_CLI_Setup_Windows_11.md).

---

## OpenRouter

**URL:** https://openrouter.ai

A routing layer that aggregates many providers. Has a set of permanently free models tagged with `:free`.

- **Sign-up:** Free account, no credit card needed to sign up or to call `:free` models at a $0 balance
- **Zero-priced models as of 2026-11-18 (17 of 478 total, verified against the public `/api/v1/models` endpoint).** Two are still Google's Lyria music models, which show $0 token prices but bill $0.04 per clip, so they are not free. That leaves **15**:
  - `baidu/ernie-5.5-flash-preview:free` (1,024,000, **new 2026-11-10**, Baidu's coding and agent model. A "preview", so expect it to be temporary)
  - `inclusionai/ling-3.2-flash:free` (1,048,576, text + image, **new 2026-11-13**)
  - `particle-ai/particle-1:free` (1,048,576, text + image, **new 2026-10-28**, an agentic coding model post-trained on DeepSeek V4.1 Flash)
  - `liquid/lfm-3-1.7b:free` (262,144, **new 2026-11-06**; output capped at 8,192; the listing says prompts may be retained)
  - `nvidia/nemotron-3-ultra-550b-a55b:free`, `nvidia/nemotron-3.5-super-vl:free`, `nvidia/nemotron-3.5-lightning:free` (1,000,000 each)
  - `nvidia/nemotron-3-nano-omni-30b-a3b-reasoning:free` (256,000)
  - `recursive/flint-v1.0:free` (262,144, a 40B / 2.5B-active coding model, also runnable locally under [Ollama](Ollama_Local_AI_Windows_11.md))
  - `google/gemma-4-31b-it:free` and `gemma-4-26b-a4b-it:free` (262,144 each)
  - `dots-studio/dots-3-note-preview:free` (512,000), `cohere/north-mini-code:free` (256,000)
  - `thinkingmachines/inkling-2-small:free` (1,048,576, **new 2026-11-09**, but restricted to agent apps, see below)
  - `openrouter/free` (200,000, OpenRouter's own auto-router across free endpoints)
- **Tool calls checked on 2026-11-18:** ERNIE 5.5 Flash Preview (0.8s), Particle-1 (0.8s), Ling 3.2 Flash (1.7s) and Flint (0.9s) all returned correct tool calls.
- **OpenAI-compatible:** Yes — `https://openrouter.ai/api/v1`
- **Key prefix:** `sk-or-`
- **Rate limits:** 20 requests/min always, plus **50 requests/day on an unfunded account** *(not re-checked in November: the docs table renders client-side and this repo's key is funded)*. `GET /api/v1/key` now returns `free_model_daily_requests` with `used`, `limit` and `remaining`, so you can see your own daily ceiling (it showed `limit: 1000` on a funded key).
- **Standout feature:** A single API key works across dozens of models and providers, which makes it the easiest way to test many models without juggling keys

> **Eight free models from October are gone.** `thinkingmachines/inkling:free` and `inkling-small:free`, `nvidia/nemotron-3-super-120b-a12b:free` and `nvidia/nemotron-3.5-content-safety:free` lost their free endpoints (paid versions remain). `poolside/laguna-s-2.1:free`, `laguna-xs-2.1:free`, `xingchen-agi/xing4.0-29b-a4b:free` and `liquid/lfm-2.5-2.6b:free` left OpenRouter entirely. Laguna XS 2.1 is still free on NVIDIA NIM. `qwen/qwen3.8-27b` is still paid only here; Groq still lists it free (docs, 2026-11-18).

> **New: a free model that refuses plain API calls.** `thinkingmachines/inkling-2-small:free` is zero-priced, but a direct call on 2026-11-18 returned `403`: *"only available on agentic harnesses. Try plugging it into a coding agent or productivity app listed on https://openrouter.ai/apps"*. Zero-priced is necessary, not sufficient.

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

Mistral's own API platform. Offers a free experimentation tier. *(Not re-verified in the October or November passes.)*

- **Sign-up:** Free account, no credit card for free tier (phone verification required)
- **Notable free models:** Mistral Small, Codestral, open-weight models
- **OpenAI-compatible:** Yes — `https://api.mistral.ai/v1`
- **Rate limits:** 1 request/sec, 500K tokens/min, 1B tokens/month on the Experimentation tier
- **Docs:** https://docs.mistral.ai

---

## Cohere

**URL:** https://dashboard.cohere.com

Specializes in text generation, embeddings, and retrieval. Good free trial keys for non-commercial use. *(Not re-verified in the October or November passes.)*

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

If you were using SambaNova for large open models, the nearest free substitutes are NVIDIA NIM (`nvidia/nemotron-3-ultra-550b-a55b`, `mistralai/mistral-large-4-1t-a52b`) and Groq (`openai/gpt-oss-120b`).

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

By 2026-09-20, `stealth/union-alpha` returned **`404` from OpenRouter's model API**. The model was revealed as **Pareto**, from Unbiased, and is **paid**. As of 2026-11-18 (unchanged since 2026-10-24) there are two dated versions: `unbiased/pareto-26.9` at $2.50 per million input tokens (262,144 context) and a newer `unbiased/pareto-26.10` at $0.80 input / $3.20 output with a 1,048,576 context.

So the free window lasted a few days rather than the advertised week, which is the whole point of listing it here. **A stealth-model promotion is a vendor buying evaluation data, not a free tier.** If you build a config around one, you are writing code with a deadline you were never told. Enjoy them while they run, but keep a real free provider configured underneath, and check `GET /api/v1/models` before assuming one is still live, because that call is public and takes a second.

---

## Tips for Using Free APIs

**Verify model IDs before you trust a guide, including this one**
Free hosted models churn fast enough that any written guide is partly stale the week it is published. The cheapest check is a one-token request against the endpoint. A `410` tells you the model is retired and usually names the EOL date; a `404` tells you the slug is wrong. Both take a second to test and save a long debugging session.

**Time your calls, not just their status codes**
Two months running, an NVIDIA model passed every error check and was still unusable: `deepseek-v4-flash-0731` in September and `deepseek-v4.1-flash` in October, each taking around three minutes per reply. In November it was October's fast pick, `minimax-m3.1-flash`, which went from under a second to about a minute. A model's latency can change a hundredfold between monthly checks, and an agent with a normal timeout presents that as its own hang.

**A retirement can land days after a review**
GLM-5.3 was re-verified on 2026-10-24 and retired on 2026-10-28. Before you trust any model ID, here or anywhere, send it one request. A `410` names the date.

**Re-check providers you have stopped checking**
SambaNova's no-card path ended around August 14 and this guide carried it for two more months under a "not re-checked" label. A provider you are not actively verifying can be wrong for longer than you think.

**Rotate providers to avoid rate limits**
Most tools (Aider, OpenCode, and similar) let you switch models mid-session. If you hit a rate limit on one provider, switch to another. Groq is best for speed, NVIDIA NIM for variety and context length, OpenRouter for trying many models through one key. One exception: Groq's free per-minute token cap is smaller than OpenCode's system prompt, so Groq is not a fallback for OpenCode on the free tier.

**Prefer OpenAI-compatible providers**
Tools like Aider, OpenCode, and Codex work out of the box with any OpenAI-compatible endpoint. Set `OPENAI_BASE_URL` and `OPENAI_API_KEY` and you are done.

**Watch for context window differences**
Free-tier context limits vary widely. Nemotron 3.5 Super VL, MiniMax M3.1 Flash, Kimi K3.1 Pro, Gemini Flash and Gemini 4 Hydrogen all reach 1M. Nemotron 3 Ultra sits at 262K on NIM (1M on OpenRouter's free endpoint). Groq caps at 131K.

**Check the daily cap, not just the per-minute one**
The requests-per-minute number is rarely what stops you. The daily ceiling is: OpenRouter at 50/day unfunded, Groq at 1,000/day on the GPT-OSS and Qwen models, Google at whatever AI Studio shows for your project. Budget against that.

**Keep API keys out of code**
Store keys as environment variables or in config files outside your git repos. Never commit them. Many providers (NVIDIA, Groq, Gemini) will rotate or revoke keys that get leaked publicly. Note that OpenCode's config file stores keys in plain text, so keep that file out of any repo. OpenCode 1.x also keeps a second, separate credential store at `~/.local/share/opencode/auth.json` for anything added via `opencode auth login`; `opencode auth list` shows what is in it.

**Track your key expiry dates**
NVIDIA keys now expire on a schedule you choose at generation time. Set a calendar reminder, or pick "never expires" if the account is only used for personal projects. An expired key produces a `403` that reads like a model problem rather than an auth problem.

**Assume free tiers shrink**
Every provider in this guide that changed its free tier during 2026 made it smaller, not larger, and several of them (Cerebras, SambaNova, Groq, and in November NVIDIA's catalog, 85 models down to 32) did so with little warning. If a free tier is load-bearing for something you care about, have a second provider configured before you need it.

---

## Datacenter Location vs. Model Origin

The direct providers listed in this guide operate US or EU-based datacenters, so your prompts and data are processed on Western infrastructure. However, several of the strongest open-weight models were created by Chinese organizations:

| Model | Made by | Served via (in this guide) | Data goes to |
|---|---|---|---|
| MiniMax M3.1 Flash | MiniMax (China) | NVIDIA NIM (US) | US |
| Step 5 Preview | StepFun (China) | NVIDIA NIM (US) | US |
| Kimi K3.1 Pro | Moonshot AI (China) | NVIDIA NIM (US) | US |
| Qwen 3.8 / 3.6 / 3.5 | Alibaba (China) | Groq (US) or Ollama (local) | Local or US |
| ERNIE 5.5 Flash Preview | Baidu (China) | OpenRouter | Upstream provider reported as "Baidu" on 2026-11-18 |
| Ling 3.2 Flash | inclusionAI / Ant Group (China) | OpenRouter | Upstream provider reported as "Ant Digital" on 2026-11-18 |
| Particle-1 | Particle AI (post-trained on DeepSeek V4.1 Flash) | OpenRouter | Depends on the upstream provider OpenRouter routes to |

The model weights being of Chinese origin does not mean your data leaves US/EU infrastructure when using the providers above. The distinction matters if you are working with sensitive or proprietary code and your compliance team has views on either model lineage or data location. OpenRouter is a router, so for any model there, check which upstream provider served the request. For ERNIE and Ling, the provider in the response was the model's own vendor, so assume your data may leave US/EU infrastructure (where those endpoints are hosted was not verified).

**One exception to be aware of:** DeepSeek offers their own direct API at `api.deepseek.com`. That endpoint is hosted in China, so data sent there goes to Chinese servers. The guides in this repo do not use that endpoint, but it is worth knowing if you encounter it elsewhere.
