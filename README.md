# AI Tools and Notes

A collection of free and open-source AI CLI tools, setup guides, and notes. Everything here uses free tiers, open models, or open-source tooling, with no paid subscriptions required.

*Last updated: August 2026.*

---

## Contents

### Setup Guides

| Guide | Description |
|---|---|
| [Free AI API Keys](Free_AI_API_Keys.md) | Reference guide to free AI API providers: NVIDIA, Groq, Google, SambaNova, OpenRouter, and more |
| [Ollama — Local AI (Windows 11)](Ollama_Local_AI_Windows_11.md) | Run AI models locally with no API key, no rate limits, no internet required |
| [LM Studio — Local AI (Windows 11)](LM_Studio_Local_AI_Windows_11.md) | GUI app for running local AI models with a built-in OpenAI-compatible API server |
| [Aider + NVIDIA NIM](Aider_Setup_NVIDIA_NIM.md) | Set up Aider AI pair-programmer with NVIDIA's free 100+ model catalog (DeepSeek V4 Flash 0731, GLM-5.2, MiniMax M3) |
| [OpenCode + NVIDIA NIM](OpenCode_Setup_NVIDIA_NIM.md) | Set up OpenCode (TUI, desktop, or IDE) with NVIDIA's free 100+ model catalog |
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
| To call AI from your own scripts | Pick a provider from the [Free AI API Keys](Free_AI_API_Keys.md) list |

---

## What's New (August 2026)

Model IDs and rate limits below were verified against live API calls on 2026-08-20, not taken from vendor blog posts.

- **NVIDIA retired DeepSeek V4 Flash and V4 Pro on 2026-08-07.** Both now return `410 Gone`. These were the headline recommendation in the previous version of these guides, so anyone following the July instructions has a dead config. The successor is **`deepseek-ai/deepseek-v4-flash-0731`**, and it is an upgrade rather than a sidegrade: same 284B/13B-active architecture and 1M context, but re-post-trained for agentic work, scoring 50 on the Artificial Analysis Intelligence Index against 40 for the model it replaces.
- **Kimi K3 landed on NVIDIA NIM** as `moonshotai/kimi-k3`. At 2.8T parameters it is the largest open-weight model released to date, with a 1M-token context window and native multimodality. **But it is not currently dependable there:** it answered normally on the morning of 2026-08-20 and then returned `404` with an empty body and an `Nvcf-Status: errored` header for the rest of the day. That is a backend function failure, not the per-account registration gate, so clicking "Try API" does not help. Confirmed still failing on re-tests at 13:44 and 13:58 that day. Retry it periodically; do not make it your default. `deepseek-ai/deepseek-v4-flash-0731` answered every request all day and is the recommendation.
- **GLM-5.2's model ID was wrong in every previous version of these guides.** It is `z-ai/glm-5.2`, not `zai-org/glm-5.2` (the latter returns a bare `404 page not found`). Its context window is **1M**, not the ~200K previously listed.
- **Cerebras is no longer a free provider.** The open free tier ended **2026-08-17**. Accounts moved to a credit model that requires adding a payment method to unlock $5 in credits, which then expire after 30 days. That fails this repo's "ongoing free tier, no credit card" bar, so Cerebras has moved to the retired list.
- **Groq dropped every Llama chat model.** A live catalog call now returns 13 models, and the only Meta entries left are `llama-prompt-guard-2` safety classifiers. `llama-3.3-70b-versatile` and `llama-3.1-8b-instant`, both recommended here in July, are gone. Groq's usable free chat models are now `openai/gpt-oss-120b`, `openai/gpt-oss-20b`, `qwen/qwen3.6-27b`, and the `groq/compound` pair.
- **Qwen3 Coder 480B and Llama 4 Maverick are gone from NVIDIA too** (EOL 2026-06-11 and 2026-07-27). Both were listed as live in the July refresh, which means that refresh shipped two already-dead model IDs. Hence the live-verification pass this time.
- **New on NVIDIA NIM this cycle:** `minimaxai/minimax-m3` (428B MoE, 1M context, multimodal), `stepfun-ai/step-3.7-flash` (198B MoE, 256K, vision), `nvidia/nemotron-3-ultra-550b-a55b`, and `nvidia/nemotron-3.5-lightning-30b-a3b`.
- **NVIDIA API keys now carry an expiry you pick at generation time.** An expired key is easy to misdiagnose, because NVIDIA returns `403 Authorization failed` on every *live* model while *retired* models still return their `410 Gone` notice. See the [403 vs 410 diagnostic](Free_AI_API_Keys.md#nvidia-nim).
- **Aider has not shipped a release since 0.86.2** (2026-02-12), and the last commit to `main` was 2026-05-22. Still usable, still the best git-native option, but treat it as maintenance-mode software.
- **OpenCode is at v1.18.19** and has passed **199K stars**, up from 160K in July.
- **Name your OpenCode provider key something other than `nvidia`.** OpenCode merges your provider config into its built-in models.dev catalog whenever the key matches a provider that catalog already knows. A config listing 4 curated NVIDIA models produced **99** entries in the model picker, including four retired IDs that return `410 Gone` the moment you select one. Renaming the key to `nim` suppresses the merge and the picker shows exactly your 4. Both behaviours verified on 2026-08-20; the [OpenCode guide](OpenCode_Setup_NVIDIA_NIM.md#4-configure-nvidia-nim-as-a-provider) has the details.
- **OpenCode stores API keys in two unrelated places.** `apiKey` in your config file, and `opencode auth login` credentials in `~/.local/share/opencode/auth.json`. The trap this creates: because NVIDIA hosts Kimi, it looks sensible to run `opencode auth login`, pick **Moonshot AI**, and paste your `nvapi-` key. That credential is used against `api.moonshot.ai`, which rejects NVIDIA keys with a `401`, so it can never work — and it sits in a file you are not looking at while you debug your config. Everything NVIDIA-hosted goes through the NIM provider block instead. Check what you have stored with `opencode auth list`.
- **OpenCode reads both `config.json` and `opencode.json`** from `~/.config/opencode/`, and merges them if both exist, with no warning that two files are in play. Keep one.
- **LM Studio 0.4.16** added tensor parallelism (splitting a model across multiple GPUs) and stable MTP speculative decoding. It has also been free for commercial use since July 2025, which the previous version of that guide understated as "free for personal use."

> **General lesson, again:** every free tier tracked in this repo that changed during 2026 got smaller, and this month two of them (Cerebras, and Groq's entire Llama line) shrank without an announcement most people saw. Verify model IDs with a live API call before building on them. `410` and `404` are cheap to test for and will save you a genuinely confusing debugging session.

### Considered and excluded this cycle

- **[Grok Bot](https://x.ai/news/introducing-grok-bot)** (xAI, announced 2026-08-11). Persistent agents that each get their own cloud computer with a browser, filesystem, and terminal, sign into your existing tools, and work unsupervised. Genuinely interesting, but it fails this repo's criteria on three counts: access requires SuperGrok Heavy ($300/mo), Cursor Ultra ($200/mo), or Cursor Teams Premium ($120/seat/mo); it is closed source; and it is a desktop and iOS product rather than a CLI or an OpenAI-compatible endpoint you can point Aider or OpenCode at.
- **xAI's API in general.** New accounts get $25 in promotional credits that expire after 30 days, with no ongoing free allowance. That is trial credit, not a free tier. xAI also retired eight models in May 2026, including `grok-3`, `grok-4-fast`, and `grok-code-fast-1`, so older configs pointing at those will fail.

---

## Philosophy

This repo exists to document free or accessible AI tooling: the kind of thing that doesn't require a credit card or a subscription to get started. Contributions, corrections, and additions welcome.

---

## Free Resources Covered

- **[NVIDIA NIM](https://build.nvidia.com)** — Free API access to 100+ frontier models (DeepSeek V4 Flash 0731, GLM-5.2, MiniMax M3, Nemotron 3, GPT-OSS, Kimi K3). Best free option available, by a wide margin.
- **[Aider](https://aider.chat)** — Open-source AI pair-programmer, works with any OpenAI-compatible API
- **[OpenCode](https://opencode.ai)** — Open-source (MIT) AI coding agent: TUI, desktop app, and IDE extension
- **[Antigravity CLI](https://antigravity.google)** — Google's closed-source successor to Gemini CLI (`agy`). Small free quota.
- **[Groq](https://groq.com)** — Fastest free inference API (LPU hardware), no credit card. Catalog is much smaller than it was.
- **[Google AI Studio](https://aistudio.google.com)** — Free Gemini Flash API access with a 1M-token context window
- **[SambaNova](https://cloud.sambanova.ai)** — Free developer tier, no credit card. MiniMax M2.7, DeepSeek V3.1, Llama 3.3 70B, GPT-OSS 120B.
- **[OpenRouter](https://openrouter.ai)** — Single API key across many providers; 50 req/day free, 1,000 after a one-time $10
- **[Ollama](https://ollama.com)** — Run open-source models locally, fully offline, OpenAI-compatible API
- **[LM Studio](https://lmstudio.ai)** — GUI app for local AI, built-in model browser, OpenAI-compatible API server

> **Retired:**
> - [Gemini CLI](https://github.com/google-gemini/gemini-cli) — shut down for free and consumer accounts on 2026-06-18.
> - [Cerebras](https://cloud.cerebras.ai) free tier — ended 2026-08-17, replaced by a payment-method-gated $5 trial credit that expires after 30 days.
