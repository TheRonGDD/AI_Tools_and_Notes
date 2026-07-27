# AI Tools and Notes

A collection of free and open-source AI CLI tools, setup guides, and notes. Everything here uses free tiers, open models, or open-source tooling — no paid subscriptions required.

*Last updated: July 2026.*

---

## Contents

### Setup Guides

| Guide | Description |
|---|---|
| [Free AI API Keys](Free_AI_API_Keys.md) | Reference guide to free AI API providers — NVIDIA, Groq, Google, Cerebras, SambaNova, and more |
| [Ollama — Local AI (Windows 11)](Ollama_Local_AI_Windows_11.md) | Run AI models locally with no API key, no rate limits, no internet required |
| [LM Studio — Local AI (Windows 11)](LM_Studio_Local_AI_Windows_11.md) | GUI app for running local AI models with a built-in OpenAI-compatible API server |
| [Aider + NVIDIA NIM](Aider_Setup_NVIDIA_NIM.md) | Set up Aider AI pair-programmer with NVIDIA's free 100+ model catalog (DeepSeek V4, GLM-5.2, Qwen3 Coder, etc.) |
| [OpenCode + NVIDIA NIM](OpenCode_Setup_NVIDIA_NIM.md) | Set up OpenCode (TUI, desktop, or IDE) with NVIDIA's free 100+ model catalog |
| [Antigravity CLI (Windows 11)](Antigravity_CLI_Setup_Windows_11.md) | Google's replacement for the retired Gemini CLI — install, quota reality check, and migration notes |

---

## Which Tool Should I Use?

| If you want… | Use |
|---|---|
| AI pair-programmer that edits files & makes git commits | **Aider** |
| A full coding agent (TUI, desktop app, or IDE extension) | **OpenCode** |
| A free coding agent you can use all day | **Aider** or **OpenCode** on NVIDIA NIM |
| Google's agent in a terminal | **Antigravity CLI** — but see the quota warning below |
| To run AI locally, no internet | **Ollama** (CLI) or **LM Studio** (GUI) |
| To call AI from your own scripts | Pick a provider from the [Free AI API Keys](Free_AI_API_Keys.md) list |

---

## What's New (July 2026)

The free-tier landscape moved a lot in mid-2026, and mostly in one direction. Highlights:

- **Gemini CLI is dead for consumers.** It stopped serving free, Pro, and Ultra accounts on **June 18, 2026**, replaced by the closed-source, Go-based [Antigravity CLI](Antigravity_CLI_Setup_Windows_11.md) (binary: `agy`). Enterprise Code Assist licenses were unaffected. The free quota regressed hard: from 1,000 requests/day to roughly **20 agent requests/day** on a compute-based weekly cap.
- **Cerebras gutted its free tier** (May 31, 2026) from about a dozen models down to **two** (`gpt-oss-120b`, `glm-4.7`), at 5 RPM instead of 30.
- **OpenRouter free accounts dropped to 50 requests/day.** A one-time $10 credit purchase permanently raises it to 1,000/day.
- **NVIDIA NIM is now the clear best free option** — 120+ models, ~40 RPM, up to 1M context. Signup now requires phone verification, and some model families need a one-time "Try API" click before your key works on them.
- **New models worth knowing:** GLM-5.2, Qwen3.5 / Qwen3.6, Gemma 4, DeepSeek V4 Pro, Nemotron 3 Super 120B.
- **Aider is at 0.86.2** and still requires **Python 3.10–3.12** (not 3.13+, despite what some guides claim).
- **OpenCode is at v1.18.x**, past 160K stars, and is no longer terminal-only — it now ships a desktop app and IDE extension alongside the TUI.

> **General lesson from this refresh:** every free tier in this repo that changed in 2026 got smaller. If a free tier is load-bearing for you, configure a backup provider before you need it.

---

## Philosophy

This repo exists to document free or accessible AI tooling — the kind of stuff that doesn't require a credit card or a subscription to get started. Contributions, corrections, and additions welcome.

---

## Free Resources Covered

- **[NVIDIA NIM](https://build.nvidia.com)** — Free API access to 120+ frontier models (DeepSeek V4, GLM-5.2, Qwen3.5, Kimi K2.6, Llama 4, Nemotron, and more). Best free option available.
- **[Aider](https://aider.chat)** — Open-source AI pair-programmer, works with any OpenAI-compatible API
- **[OpenCode](https://opencode.ai)** — Open-source (MIT) AI coding agent: TUI, desktop app, and IDE extension
- **[Antigravity CLI](https://antigravity.google)** — Google's closed-source successor to Gemini CLI (`agy`). Small free quota.
- **[Groq](https://groq.com)** — Fastest free inference API (LPU hardware), no credit card, every model on the free tier
- **[Google AI Studio](https://aistudio.google.com)** — Free Gemini 3.5 Flash API access with a 1M-token context window
- **[Cerebras](https://cloud.cerebras.ai)** — Very fast inference, 1M tokens/day, but only two free models since May 2026
- **[SambaNova](https://cloud.sambanova.ai)** — Free access to Llama 4, DeepSeek V3.1, and other large models
- **[OpenRouter](https://openrouter.ai)** — Single API key for many providers; 50 req/day free, 1,000 after a one-time $10
- **[Ollama](https://ollama.com)** — Run open-source models locally, fully offline, OpenAI-compatible API
- **[LM Studio](https://lmstudio.ai)** — GUI app for local AI, built-in model browser, OpenAI-compatible API server

> **Retired:** [Gemini CLI](https://github.com/google-gemini/gemini-cli) — shut down for free and consumer accounts on June 18, 2026.
