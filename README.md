# AI Tools and Notes

A collection of free and open-source AI CLI tools, setup guides, and notes. Everything here uses free tiers, open models, or open-source tooling, with no paid subscriptions required.

*Last updated: November 2026.*

---

## Contents

### Setup Guides

| Guide | Description |
|---|---|
| [Free AI API Keys](Free_AI_API_Keys.md) | Reference guide to free AI API providers: NVIDIA, Groq, Google, OpenRouter, and more |
| [Ollama — Local AI (Windows 11)](Ollama_Local_AI_Windows_11.md) | Run AI models locally with no API key, no rate limits, no internet required |
| [LM Studio — Local AI (Windows 11)](LM_Studio_Local_AI_Windows_11.md) | GUI app for running local AI models with a built-in OpenAI-compatible API server, plus Bionic, its local coding agent |
| [Aider + NVIDIA NIM](Aider_Setup_NVIDIA_NIM.md) | Set up Aider AI pair-programmer with NVIDIA's free model catalog (Nemotron 3 Ultra, Mistral Large 4, Nemotron 3.5) |
| [OpenCode + NVIDIA NIM](OpenCode_Setup_NVIDIA_NIM.md) | Set up OpenCode (TUI, desktop, or IDE) with NVIDIA's free model catalog, plus what changes in OpenCode 2 |
| [Antigravity CLI (Windows 11)](Antigravity_CLI_Setup_Windows_11.md) | Google's replacement for the retired Gemini CLI: install, quota reality check, and migration notes |

---

## Which Tool Should I Use?

| If you want… | Use |
|---|---|
| AI pair-programmer that edits files and makes git commits | **Aider** (or its actively developed fork, [cecli](Aider_Setup_NVIDIA_NIM.md)) |
| A full coding agent (TUI, desktop app, or IDE extension) | **OpenCode** |
| A free coding agent you can use all day | **Aider** or **OpenCode** on NVIDIA NIM |
| Google's agent in a terminal | **Antigravity CLI**, but see the quota warning first (or its untested [Gemini API key mode](Antigravity_CLI_Setup_Windows_11.md#using-a-gemini-api-key-instead)) |
| To run AI locally, no internet | **Ollama** (CLI) or **LM Studio** (GUI) |
| A coding agent that runs entirely on local models | **LM Studio Bionic** (GUI) — see the [LM Studio guide](LM_Studio_Local_AI_Windows_11.md#8-lm-studio-bionic) |
| To call AI from your own scripts | Pick a provider from the [Free AI API Keys](Free_AI_API_Keys.md) list |

---

## What's New (November 2026)

Every NVIDIA and OpenRouter model ID below was checked with a live API call on 2026-11-18, and every NVIDIA model recommended here was also sent a real tool call. Groq and Google come from their own documentation only this month (no key was available for a live call). Tool versions come from GitHub, npm, PyPI, Scoop, and each vendor's own release manifest or changelog. October's report is in the git history.

### What got worse

- **⚠️ GLM-5.3, the default model in every guide here, is retired on NVIDIA NIM.** `z-ai/glm-5.3` returns `410 Gone` with an end-of-life date of **2026-10-28**, four days after October's review re-verified it. If you pasted any config or command from this repo, it is broken. The new default is `nvidia/nemotron-3-ultra-550b-a55b`; see the [OpenCode](OpenCode_Setup_NVIDIA_NIM.md) and [Aider](Aider_Setup_NVIDIA_NIM.md) guides.
- **NVIDIA's free catalog fell from 85 models to 32.** Also retired since October: `openai/gpt-oss-20b` (EOL 2026-11-18), `deepseek-ai/deepseek-v4.1-flash` (2026-11-16) and `moonshotai/kimi-k2.6` (2026-11-15). The "80+ models" figure this repo quoted all year is gone.
- **MiniMax M3.1 Flash is no longer fast.** October's 0.2-to-0.7-second pick took 48 to 75 seconds per reply on 2026-11-18. It still answers correctly, but it is out of the configs and no longer the Aider editor model.
- **OpenRouter's free list fell from 18 to 15.** Eight left (both Inkling models, both Laguna models, Nemotron 3 Super, Xing4.0 and two others) and five arrived. One of the new ones, `inkling-2-small:free`, returns `403` on a plain API call: it only works inside agent apps.
- **`scoop install opencode` now installs OpenCode 2** (since 2026-11-11). For 1.x, use `scoop install versions/opencode1`. This repo's `opencode.json` works on both.

### What got better

- **Nemotron 3 Ultra is the new default, and it is fast:** 0.8 to 1.9 seconds per reply and 1.0s for a correct tool call. The Nemotron 3.5 family, Gemma 4 31B and Laguna XS 2.1 also made correct tool calls in under 2 seconds.
- **New strong model on NIM: Mistral Large 4** (`mistralai/mistral-large-4-1t-a52b`, 1T / 52B active). Correct tool calls in 2.8 to 4.8s. It is the planner in Aider's new [architect-mode](Aider_Setup_NVIDIA_NIM.md#architect-mode-two-model-workflow) pairing. `moonshotai/kimi-k3.1-pro`, successor to the retired Kimi K3, also arrived, but takes about two minutes per reply.
- **Step 5 Preview and Gemma 4 31B recovered on NIM.** October measured 134 seconds or a timeout for both; both answered in seconds this month.
- **Google added Gemini 4 Hydrogen to its free tier** (2026-11-12, 1M context; docs only), plus `gemini-decision-1`, a free decision/classifier model with published limits of 10,000 requests/day. And `gemini-3.7-flash` / `gemini-3.5-flash` are not dying after all: they now route to 3.8 Flash and 3.6 Flash.
- **New free OpenRouter models:** Baidu's `ernie-5.5-flash-preview:free`, inclusionAI's `ling-3.2-flash:free`, Particle AI's `particle-1:free` (all 1M context, all made correct tool calls) and Liquid's tiny `lfm-3-1.7b:free`.
- **Google settled the Antigravity quota question:** its plans page says free accounts refresh **weekly**; the 5-hour refresh is for Pro and Ultra.
- **Antigravity CLI can run on a Gemini API key** instead of the small account quota, and has been able to since August. This repo missed it. Now [documented](Antigravity_CLI_Setup_Windows_11.md#using-a-gemini-api-key-instead), untested with a free key.
- **New local models under Ollama:** Liquid's LFM3 (1.7B, 1.1GB, built for tool calling), Xiaomi's MiMo V2.6 (9B, 6.6GB) and Ripple Preview (65B, 38.8GB). Every existing model size re-measured unchanged.

### What changed shape

- **Correction to October:** the "registration gate reaches models that already worked" finding was probably a misdiagnosis. Two of the three models October blamed on the gate now return `410` with EOL dates *before* October's test, so NVIDIA's `Not found for account` can also mean "recently retired".
- **OpenCode 2 reached 2.3.2.** Subagents now run in the background and background compaction is on by default (2.3.1), which costs requests on a free tier. The 1.x desktop app (1.19.6) now offers the upgrade to 2. OpenCode's main docs URL serves V2; V1 docs moved to `opencode.ai/v1/docs`.
- **Antigravity CLI 1.4.7 → 1.5.11:** Auto is the default Agent Mode where available (still not on Windows), `agy acp` turns it into an editor agent server, and a batch of Windows fixes landed (BOM-encoded settings files, PowerShell quoting, IPv6 sign-in).
- **Ollama 0.40.11 → 0.40.16**, LM Studio 0.4.26 → 0.4.28, and **Bionic 1.1.9 → 1.2.1**, which adds experimental support for external API models.
- **Aider still has not moved:** 0.86.2, last commit 2026-05-22. Its fork **cecli** reached 1.13.2 and made agent mode its default.

> **General lesson this month:** a retirement can land days after a review. GLM-5.3 was re-verified on 2026-10-24 and retired on 2026-10-28. Before you trust any model ID in this repo, send it one request. A `410` names the date.

### Considered and excluded this cycle

- **Gemini 4 Carbon and Gemini 4 Argon.** Both "Not available" on Google's free tier.
- **Inkling 2 Small on OpenRouter.** Zero-priced, but a direct API call returns `403` ("only available on agentic harnesses"). A usage restriction, not an open free tier.
- **MiniMax M3.1 Flash and Kimi K3.1 Pro as defaults.** Both work on NIM, but at one to two minutes per reply neither earns a recommended slot.
- **Step 5 Preview on NIM.** Much better than October, but a 36-second tool call keeps it on the watch list.
- **OpenCode Zen's rotating free models and OpenRouter stealth models.** Promotional windows, not a free tier.
- **Pareto (formerly Union Alpha).** Unchanged: `unbiased/pareto-26.10` at $0.80/M input and $3.20/M output. Still paid.
- **Groq's new preview listings** (`minimaxai/minimax-m2.7`, `meta-models/muse-spark-open`). On its models page, but Enterprise-only or contact-sales, not the free tier.
- **LM Studio Secure Cloud.** Requires billing (not re-checked). Bionic itself stays, because local models cost nothing.

---

## Philosophy

This repo exists to document free or accessible AI tooling: the kind of thing that doesn't require a credit card or a subscription to get started. Contributions, corrections, and additions welcome.

---

## Free Resources Covered

- **[NVIDIA NIM](https://build.nvidia.com)** — Free API access to about 30 models (Nemotron 3 Ultra and 3.5, Mistral Large 4, Gemma 4, Muse Glimmer). Still the best free option for agents, though the catalog shrank from 85 to 32 in November and GLM-5.3 is gone.
- **[Aider](https://aider.chat)** — Open-source AI pair-programmer, works with any OpenAI-compatible API. In maintenance mode; [cecli](https://github.com/cecli-dev/cecli) is an active fork
- **[OpenCode](https://opencode.ai)** — Open-source (MIT) AI coding agent: TUI, desktop app, and IDE extension. 1.x and 2.x both current
- **[Antigravity CLI](https://antigravity.google)** — Google's closed-source successor to Gemini CLI (`agy`). Small free quota, or bring a Gemini API key.
- **[Groq](https://groq.com)** — Fastest free inference API (LPU hardware), no credit card. Every chat model under 0.3s when last tested live (2026-10-24); free-plan limits unchanged in its docs on 2026-11-18.
- **[Google AI Studio](https://aistudio.google.com)** — Free Gemini API access with a 1M-token context window, now including Gemini 4 Hydrogen and Gemini 3.8 Flash
- **[OpenRouter](https://openrouter.ai)** — Single API key across many providers; 15 free models (2026-11-18); 50 req/day free, 1,000 after a one-time $10
- **[Ollama](https://ollama.com)** — Run open-source models locally, fully offline, OpenAI-compatible API
- **[LM Studio](https://lmstudio.ai)** — GUI app for local AI, built-in model browser, OpenAI-compatible API server. **Bionic**, its agent app, is free on local models

> **Retired:**
> - [Gemini CLI](https://github.com/google-gemini/gemini-cli) — shut down for free and consumer accounts on 2026-06-18.
> - [Cerebras](https://cloud.cerebras.ai) free tier — ended 2026-08-17, replaced by a payment-method-gated $5 trial credit that expires after 30 days.
> - [SambaNova](https://cloud.sambanova.ai) free tier — now requires a payment method before your first request (around 2026-08-14; caught in the October 2026 review).
