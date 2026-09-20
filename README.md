# AI Tools and Notes

A collection of free and open-source AI CLI tools, setup guides, and notes. Everything here uses free tiers, open models, or open-source tooling, with no paid subscriptions required.

*Last updated: September 2026.*

---

## Contents

### Setup Guides

| Guide | Description |
|---|---|
| [Free AI API Keys](Free_AI_API_Keys.md) | Reference guide to free AI API providers: NVIDIA, Groq, Google, SambaNova, OpenRouter, and more |
| [Ollama — Local AI (Windows 11)](Ollama_Local_AI_Windows_11.md) | Run AI models locally with no API key, no rate limits, no internet required |
| [LM Studio — Local AI (Windows 11)](LM_Studio_Local_AI_Windows_11.md) | GUI app for running local AI models with a built-in OpenAI-compatible API server, plus Bionic, its new local coding agent |
| [Aider + NVIDIA NIM](Aider_Setup_NVIDIA_NIM.md) | Set up Aider AI pair-programmer with NVIDIA's free 80+ model catalog (GLM-5.3, Nemotron 3, Kimi K3) |
| [OpenCode + NVIDIA NIM](OpenCode_Setup_NVIDIA_NIM.md) | Set up OpenCode (TUI, desktop, or IDE) with NVIDIA's free 80+ model catalog |
| [Antigravity CLI (Windows 11)](Antigravity_CLI_Setup_Windows_11.md) | Google's replacement for the retired Gemini CLI: install, quota reality check, and migration notes |

---

## Which Tool Should I Use?

| If you want… | Use |
|---|---|
| AI pair-programmer that edits files and makes git commits | **Aider** |
| A full coding agent (TUI, desktop app, or IDE extension) | **OpenCode** |
| A free coding agent you can use all day | **Aider** or **OpenCode** on NVIDIA NIM |
| Google's agent in a terminal | **Antigravity CLI**, but see the quota warning first |
| To run AI locally, no internet | **Ollama** (CLI) or **LM Studio** (GUI) |
| A coding agent that runs entirely on local models | **LM Studio Bionic** (GUI) — see the [LM Studio guide](LM_Studio_Local_AI_Windows_11.md#8-lm-studio-bionic) |
| To call AI from your own scripts | Pick a provider from the [Free AI API Keys](Free_AI_API_Keys.md) list |

---

## What's New (September 2026)

Every model ID and error code below was verified against a live API call on 2026-09-20, not taken from vendor blog posts. This month that distinction mattered twice: once where a vendor's docs were wrong, and once where a model passed every error check and was still unusable.

- **Qwen 3.8 27B: get the model ID right, because both free hosts spell it differently.** This is the model the most people are currently trying to run, and there are three separate traps. On **Groq's free API** it is `qwen/qwen3.8-27b`, and the previous `qwen/qwen3.6-27b` ID — which this repo listed in August — **is gone from the catalog**, so any guide written before September hands you a dead ID. Running it **locally under Ollama** it is `qwen3.8:27b`, a colon rather than a hyphen, with a `qwen3.8:27b-mlx` build for Apple Silicon. And the **context window differs by host**: Ollama's library lists 256K, Groq serves it at 131,042. Groq catalog verified live 2026-09-20.
- **⚠️ Last month's headline model is now a latency trap.** `deepseek-ai/deepseek-v4-flash-0731` has **not** been retired and still returns valid `200` responses, but it has become drastically slower: four probes for a 16-token reply gave timeouts at 120s (twice), one success at **172s**, and a timeout at **300s**. Because there is no error code, an agent like Aider or OpenCode just appears to hang, and you go hunting for a config bug that does not exist. This is the nastiest kind of breakage these guides can ship, because the previous version told you to make it your default. **Switch to `z-ai/glm-5.3`**, which answered in about 4 seconds on every attempt.
- **NVIDIA's catalog shrank from 103 models to 82 in one month.** Six of the nine chat models recommended in the August guides are now `410 Gone`. Each EOL date below was read out of the `410` response body: `z-ai/glm-5.2` (2026-08-21), `meta/llama-3.3-70b-instruct` (2026-08-26), `stepfun-ai/step-3.7-flash` (2026-08-28), `openai/gpt-oss-120b` (2026-09-03), `minimaxai/minimax-m3` (2026-09-09). If you built a config from the August files, most of it is dead.
- **GLM-5.2 became GLM-5.3, and it is the new default recommendation here.** `z-ai/glm-5.3` and `z-ai/glm-5.3-flash` replaced 5.2 on NIM. The `z-ai` prefix still matters: `zai-org` returns a bare `404 page not found`. Note that 5.3 is a reasoning model, so short `max_tokens` values get consumed by reasoning before any visible content is produced — budget accordingly.
- **Kimi K3 recovered.** August's guides flagged `moonshotai/kimi-k3` as unstable after a sustained backing-function outage (`404` with an empty body and `Nvcf-Status: errored`). That is over: it answered every probe on 2026-09-20. It is slow, at 85 to 105 seconds per call, so it is a deliberate choice rather than a default. The lesson cuts both ways — a model can be listed, broken, then quietly fixed, with no announcement either way.
- **Two new NVIDIA failure codes worth knowing, both `503`.** Plain `Service temporarily overloaded` is a brief blip that clears on retry. But `ResourceExhausted: Worker local total request limit reached (78/32)` means that model's **shared** worker pool is oversubscribed across all NVIDIA users — those numbers are not your account's limits, and nothing you change on your end will fix it. Both are now in the [error-code table](Free_AI_API_Keys.md#reading-nvidias-error-codes-the-403-vs-410-diagnostic).
- **The registration gate is not just a new-key problem.** August's guides implied it mostly bites fresh keys. On 2026-09-20, on a key that had been working for a month, `moonshotai/kimi-k2.6` and `nvidia/nemotron-nano-3-30b-a3b` both returned `Function '<uuid>': Not found for account`. Click "Try API" once per model page regardless of key age.
- **LM Studio shipped a second app: Bionic.** Announced 2026-07-16 and at 1.1.5 as of 2026-09-19, Bionic is an agent rather than a chat window — Code Projects for agentic multi-file editing with inline diffs, Work Projects for sandboxed document work with web search. Free on local models; only LM Studio Secure Cloud needs billing. It is the first GUI-first local coding agent this repo covers, and it now has a [comparison against Aider and OpenCode](LM_Studio_Local_AI_Windows_11.md#bionic-vs-aider-and-opencode). LM Studio proper is at **0.4.25** (2026-09-19), up from 0.4.16.
- **Ollama is at 0.34.2**, up from 0.32.14, and one change is quietly breaking: as of **0.34.1**, `ollama create` no longer does GGUF safetensor conversion and quantization for you — that now requires llama.cpp tooling. Scripts that built GGUF models through `ollama create` will fail. Separately, 0.34.2 added a sign-in prompt on first run (you can decline and continue locally), and 0.33.0/0.34.0 added Claude Desktop and ChatGPT Desktop gateway integrations.
- **OpenCode is at v1.18.31** (2026-09-14) and past **208K stars**, up from 199K in August. The `nim` provider-key trick from last month still applies, and it matters more now: a picker merged with the built-in models.dev catalog will be offering you six NVIDIA model IDs that return `410 Gone`.
- **Aider has not moved at all.** Still 0.86.2 (2026-02-12), last commit to `main` still 2026-05-22. That is seven months without a release and four without a commit. It still works and is still the only tool here that lands its work as real git commits, but the maintenance-mode label is now firmly earned.
- **Groq's documentation is wrong about Llama, and this was worth checking twice.** August's refresh recorded, from a live call, that Groq had dropped every Llama chat model. Groq's docs page currently lists Llama 3.1 8B and Llama 3.3 70B as production models, which looked like a contradiction. A live call on 2026-09-20 settled it: **13 models, and the only Meta entries are the two `llama-prompt-guard-2` classifiers**, which have 512-token context windows and cannot hold a conversation. The API is right and the docs are stale. A config pointing at `llama-3.3-70b-versatile` or `llama-3.1-8b-instant` is broken, and Groq's own documentation will not tell you so.
- **The same model can be retired on one free provider and alive on another.** `openai/gpt-oss-120b` hit end of life on NVIDIA NIM on 2026-09-03 and returns `410 Gone` there, while running fine on Groq on 2026-09-20. Worth internalizing before you conclude a model is "gone."
- **Antigravity CLI is at 1.2.7**, up from 1.1.15, shipping releases most weekdays. Free-tier quota complaints continued through September, with users reporting every model blocked by "Individual quota reached" until a fixed date days later. That is the weekly ceiling, not a transient error.

> **General lesson, again, and sharper this month:** the August refresh's advice was to check for `410` and `404` before building on a model ID. That was not enough. `deepseek-v4-flash-0731` passes every error check and is still unusable as a default. **Time your calls, not just their status codes.** A free endpoint can degrade into uselessness without ever returning an error, and a coding agent will present that to you as its own failure.

### Considered and excluded this cycle

- **LM Studio Secure Cloud.** The hosted side of Bionic, for running frontier open-weight models you cannot fit locally. Requires billing setup, so it falls outside this repo's bar. Bionic itself stays, because local models cost nothing.
- **[Grok Bot](https://x.ai/news/introducing-grok-bot)** (xAI, announced 2026-08-11). Still excluded, unchanged from August: access requires SuperGrok Heavy ($300/mo), Cursor Ultra ($200/mo), or Cursor Teams Premium ($120/seat/mo); it is closed source; and it is a desktop and iOS product rather than a CLI or an OpenAI-compatible endpoint you can point Aider or OpenCode at.
- **xAI's API in general.** New accounts get $25 in promotional credits that expire after 30 days, with no ongoing free allowance. That is trial credit, not a free tier.

---

## Philosophy

This repo exists to document free or accessible AI tooling: the kind of thing that doesn't require a credit card or a subscription to get started. Contributions, corrections, and additions welcome.

---

## Free Resources Covered

- **[NVIDIA NIM](https://build.nvidia.com)** — Free API access to 80+ frontier models (GLM-5.3, Nemotron 3, Kimi K3, Gemma 4, Muse Glimmer, GPT-OSS). Best free option available, by a wide margin, though the catalog shrank this month.
- **[Aider](https://aider.chat)** — Open-source AI pair-programmer, works with any OpenAI-compatible API
- **[OpenCode](https://opencode.ai)** — Open-source (MIT) AI coding agent: TUI, desktop app, and IDE extension
- **[Antigravity CLI](https://antigravity.google)** — Google's closed-source successor to Gemini CLI (`agy`). Small free quota.
- **[Groq](https://groq.com)** — Fastest free inference API (LPU hardware), no credit card. 13 models, no Llama chat models, verified live 2026-09-20.
- **[Google AI Studio](https://aistudio.google.com)** — Free Gemini Flash API access with a 1M-token context window
- **[SambaNova](https://cloud.sambanova.ai)** — Free developer tier, no credit card. MiniMax M2.7, DeepSeek V3.1, Llama 3.3 70B, GPT-OSS 120B.
- **[OpenRouter](https://openrouter.ai)** — Single API key across many providers; 50 req/day free, 1,000 after a one-time $10
- **[Ollama](https://ollama.com)** — Run open-source models locally, fully offline, OpenAI-compatible API
- **[LM Studio](https://lmstudio.ai)** — GUI app for local AI, built-in model browser, OpenAI-compatible API server. **Bionic**, its agent app, is free on local models

> **Retired:**
> - [Gemini CLI](https://github.com/google-gemini/gemini-cli) — shut down for free and consumer accounts on 2026-06-18.
> - [Cerebras](https://cloud.cerebras.ai) free tier — ended 2026-08-17, replaced by a payment-method-gated $5 trial credit that expires after 30 days.
