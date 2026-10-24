# AI Tools and Notes

A collection of free and open-source AI CLI tools, setup guides, and notes. Everything here uses free tiers, open models, or open-source tooling, with no paid subscriptions required.

*Last updated: October 2026.*

---

## Contents

### Setup Guides

| Guide | Description |
|---|---|
| [Free AI API Keys](Free_AI_API_Keys.md) | Reference guide to free AI API providers: NVIDIA, Groq, Google, OpenRouter, and more |
| [Ollama — Local AI (Windows 11)](Ollama_Local_AI_Windows_11.md) | Run AI models locally with no API key, no rate limits, no internet required |
| [LM Studio — Local AI (Windows 11)](LM_Studio_Local_AI_Windows_11.md) | GUI app for running local AI models with a built-in OpenAI-compatible API server, plus Bionic, its local coding agent |
| [Aider + NVIDIA NIM](Aider_Setup_NVIDIA_NIM.md) | Set up Aider AI pair-programmer with NVIDIA's free 80+ model catalog (GLM-5.3, MiniMax M3.1 Flash, Nemotron) |
| [OpenCode + NVIDIA NIM](OpenCode_Setup_NVIDIA_NIM.md) | Set up OpenCode (TUI, desktop, or IDE) with NVIDIA's free 80+ model catalog, plus what changes in OpenCode 2 |
| [Antigravity CLI (Windows 11)](Antigravity_CLI_Setup_Windows_11.md) | Google's replacement for the retired Gemini CLI: install, quota reality check, and migration notes |

---

## Which Tool Should I Use?

| If you want… | Use |
|---|---|
| AI pair-programmer that edits files and makes git commits | **Aider** (or its actively developed fork, [cecli](Aider_Setup_NVIDIA_NIM.md)) |
| A full coding agent (TUI, desktop app, or IDE extension) | **OpenCode** |
| A free coding agent you can use all day | **Aider** or **OpenCode** on NVIDIA NIM |
| Google's agent in a terminal | **Antigravity CLI**, but see the quota warning first |
| To run AI locally, no internet | **Ollama** (CLI) or **LM Studio** (GUI) |
| A coding agent that runs entirely on local models | **LM Studio Bionic** (GUI) — see the [LM Studio guide](LM_Studio_Local_AI_Windows_11.md#8-lm-studio-bionic) |
| To call AI from your own scripts | Pick a provider from the [Free AI API Keys](Free_AI_API_Keys.md) list |

---

## What's New (October 2026)

Every NVIDIA, Groq and OpenRouter model ID below was verified against a live API call on 2026-10-24, and every NVIDIA model recommended here was also sent a real tool call, not just a chat message. Tool versions come from GitHub, npm, PyPI, and each vendor's own update or release manifest.

### What got better

- **A new fast default-class model on NVIDIA NIM: `minimaxai/minimax-m3.1-flash`.** 0.2 to 0.7 seconds per reply, 2.3s for a correct tool call, 1M context, and it takes text, image and video input. This is now the fast pick in both agent guides, and the editor model in Aider's [architect mode](Aider_Setup_NVIDIA_NIM.md#architect-mode-two-model-workflow).
- **Every fast NIM model tested can actually drive an agent.** MiniMax M3.1 Flash, Nemotron 3 Ultra, Nemotron 3.5 Lightning, Nemotron 3.5 Super VL, Nemotron 3.5 Bolt E4B, Muse Glimmer and GPT-OSS 20B all returned a correct `read_file` tool call, in 0.6 to 3.2 seconds.
- **NVIDIA's catalog grew again after September's cut**, from 82 to 85, with new models from MiniMax, StepFun and DeepSeek and new Nemotron 3.5 models, including the multimodal Super VL and the small Bolt E4B.
- **Groq is extremely fast.** Every chat model answered in 0.20 to 0.29 seconds. Qwen 3.8 27B now reports its full 131,072 context there, and Groq's docs finally agree with its API about Llama being gone.
- **New free models on OpenRouter:** NVIDIA's multimodal `nemotron-3.5-super-vl:free` at 1M context, Recursive's coding model `flint-v1.0:free`, and China Telecom's `xing4.0-29b-a4b:free`.
- **A promising new local coding model: Flint** (Apache 2.0, 40B with ~2.5B active, 22.9GB under Ollama, Windows since 0.40.9). Recursive reports 74.2% SWE-bench Verified. That is a vendor number and is not verified here, but at this size it is worth a test. Also new under Ollama: NVIDIA's Nemotron 3.5 Super, Bolt (74B, tuned for a 24GB card) and Bolt E4B (7.9GB).
- **Ollama dropped the account prompt** it had added to first-run setup in September (0.40.1), and added a whole new model category: decision models that return choices and probabilities instead of text (`/v1/systemone`). LM Studio 0.4.26 and Bionic 1.1.8 added the same endpoints.
- **Aider has an active fork.** [cecli](https://github.com/cecli-dev/cecli) (formerly aider-ce, Apache 2.0) shipped v1.9.0 on 2026-10-17, adds a TUI, MCP and agent mode, and its package has no Python 3.13 cap. Untested here, and small, but it is the first sign of life around Aider in five months.
- **Antigravity CLI got much better on Windows.** At 1.4.7, its sandbox runs without admin rights and plugin installs are reliable. It also gained `/compact`, `/effort` and `/plugin`, and it now shows quota reset times in local time.
- **Google added Gemini 3.8 Flash to its free tier.**
- **Bionic grew up.** 1.1.9 adds a shared Canvas, Office and PDF previews, and the ability to run alongside LM Studio and share its loaded models.

### What got worse

- **Both of September's slow-but-working NVIDIA models are now retired.** `moonshotai/kimi-k3` reached end of life on **2026-10-15**, and its model page marks the free endpoint "Deprecated" while the paid one stays up. `deepseek-ai/deepseek-v4-flash-0731` went on 2026-09-21. If you pasted September's OpenCode config, it contains Kimi K3: remove it.
- **DeepSeek V4.1 Flash repeats the latency trap.** The successor to last month's warning is new on NIM and just as slow: one 180s timeout, then a **166-second** reply. It returns `200`, so an agent looks hung. Second month in a row for this lineage.
- **GLM-5.3 got slower.** It is still the default and still answered every probe, but at 7 to 27 seconds per reply, against about 4 seconds in September.
- **A model that worked last month needs re-registering.** `nvidia/nemotron-3-super-120b-a12b` answered in a second on 2026-09-20 and returned `Not found for account` on the same key on 2026-10-24. The "Try API" gate is not just a new-key problem, and not just for models you have never used.
- **SambaNova is no longer free.** Its plans page now says the Free plan requires you to *"add a payment method and purchase credits to run your first requests."* A pricing tracker dates that to around 2026-08-14, so this repo carried it for two months under a "not re-checked" label. It has moved to the [retired section](Free_AI_API_Keys.md#sambanova), next to Cerebras.
- **Groq shrank from 13 models to 11.** `groq/compound` and `compound-mini` were shut down on 2026-09-21, the day after September's review listed them as live.
- **OpenRouter's free list fell from 24 to 18.** `qwen/qwen3.8-27b:free` (the full-context free host September recommended) and `z-ai/glm-5.2:free` are gone, along with five others. Two zero-priced Lyria music entries look free in the API and bill per clip.
- **OpenCode 1.19 discontinued session sharing** (existing share links are dead) and stopped installing updates automatically.
- **Ollama 0.40 re-converts your existing models in the background and keeps the originals as backups**, so a large library temporarily takes up to twice the disk.
- **Google dropped `gemini-3.7-flash` and `gemini-3.5-flash` from its pricing page** without a shutdown notice that this review could find, and its rate-limit page no longer publishes free-tier numbers at all.

### What changed shape

- **⚠️ OpenCode 2 is out, and it ignores `config.json`.** 2.0.0 shipped 2026-09-11 and is at 2.1.4. It reads only `opencode.json(c)`. That filename change was [deliberate](https://github.com/anomalyco/opencode/pull/34645), and it means a `config.json`, which this repo told you to create, silently loses all your providers on upgrade. V1 plugins also do not run, and a `CLAUDE.md` fallback is no longer read. 1.x (now 1.19.4) stays on its own line and never auto-upgrades, so nothing breaks until you opt in. The OpenCode guide now uses `opencode.json`, which works on both, and has a new [OpenCode 2 section](OpenCode_Setup_NVIDIA_NIM.md#12-opencode-2).
- **Ollama went from 0.34.2 to 0.40.11**, 17 releases in five weeks, moving Apple Silicon to MLX by default and everything else onto a new llama.cpp runner.
- **Aider still has not moved.** 0.86.2 (2026-02-12), last commit 2026-05-22. Eight months without a release.

> **General lesson this month:** re-check what you have stopped checking. SambaNova changed in August and sat in this repo, marked "not re-checked", for two months. Nemotron 3 Super worked in September and is gated in October. Every claim in these guides has a date on it for exactly this reason.

### Considered and excluded this cycle

- **Step 5 Preview on NIM** (`stepfun-ai/step-5-preview`, 600B / 27B active). Listed and genuinely free, but took 134 seconds on one call and timed out on another. Kept in the tables as a model to watch, not recommended.
- **OpenCode Zen's rotating free models** (for example "Exo Free", added to Zen's docs in October, and "Fledge Alpha Free", already removed). Same problem as stealth models: promotional windows, not a free tier.
- **Pareto (formerly Union Alpha).** Now `unbiased/pareto-26.10` at $0.80/M input and $3.20/M output with a 1M context. Cheaper than September's version, but still paid.
- **LM Studio Secure Cloud.** The hosted side of Bionic. Requires billing, so it falls outside this repo's bar. Bionic itself stays, because local models cost nothing.
- **[Grok Bot](https://x.ai/news/introducing-grok-bot) and the xAI API.** Unchanged: Grok Bot requires a $120 to $300/month plan and is closed source; the API offers 30-day trial credit, not a free tier.

---

## Philosophy

This repo exists to document free or accessible AI tooling: the kind of thing that doesn't require a credit card or a subscription to get started. Contributions, corrections, and additions welcome.

---

## Free Resources Covered

- **[NVIDIA NIM](https://build.nvidia.com)** — Free API access to 80+ frontier models (GLM-5.3, MiniMax M3.1 Flash, Nemotron 3 and 3.5, Muse Glimmer, GPT-OSS). Still the best free option available, by a wide margin.
- **[Aider](https://aider.chat)** — Open-source AI pair-programmer, works with any OpenAI-compatible API. In maintenance mode; [cecli](https://github.com/cecli-dev/cecli) is an active fork
- **[OpenCode](https://opencode.ai)** — Open-source (MIT) AI coding agent: TUI, desktop app, and IDE extension. 1.x and 2.x both current
- **[Antigravity CLI](https://antigravity.google)** — Google's closed-source successor to Gemini CLI (`agy`). Small free quota.
- **[Groq](https://groq.com)** — Fastest free inference API (LPU hardware), no credit card. 11 models, every chat model under 0.3s, verified live 2026-10-24.
- **[Google AI Studio](https://aistudio.google.com)** — Free Gemini Flash API access with a 1M-token context window, now including Gemini 3.8 Flash
- **[OpenRouter](https://openrouter.ai)** — Single API key across many providers; 18 free models; 50 req/day free, 1,000 after a one-time $10
- **[Ollama](https://ollama.com)** — Run open-source models locally, fully offline, OpenAI-compatible API
- **[LM Studio](https://lmstudio.ai)** — GUI app for local AI, built-in model browser, OpenAI-compatible API server. **Bionic**, its agent app, is free on local models

> **Retired:**
> - [Gemini CLI](https://github.com/google-gemini/gemini-cli) — shut down for free and consumer accounts on 2026-06-18.
> - [Cerebras](https://cloud.cerebras.ai) free tier — ended 2026-08-17, replaced by a payment-method-gated $5 trial credit that expires after 30 days.
> - [SambaNova](https://cloud.sambanova.ai) free tier — now requires a payment method before your first request (around 2026-08-14; caught in the October 2026 review).
