# AI Tools and Notes

A collection of free and open-source AI CLI tools, setup guides, and notes. Everything here uses free tiers, open models, or open-source tooling — no paid subscriptions required.

*Last updated: May 2026.*

---

## Contents

### Setup Guides

| Guide | Description |
|---|---|
| [Free AI API Keys](Free_AI_API_Keys.md) | Reference guide to free AI API providers — NVIDIA, Groq, Google, Cerebras, SambaNova, and more |
| [Ollama — Local AI (Windows 11)](Ollama_Local_AI_Windows_11.md) | Run AI models locally with no API key, no rate limits, no internet required |
| [LM Studio — Local AI (Windows 11)](LM_Studio_Local_AI_Windows_11.md) | GUI app for running local AI models with a built-in OpenAI-compatible API server |
| [Aider + NVIDIA NIM](Aider_Setup_NVIDIA_NIM.md) | Set up Aider AI pair-programmer with NVIDIA's free 100+ model catalog (DeepSeek V4, Qwen3 Coder, Kimi K2.6, etc.) |
| [OpenCode + NVIDIA NIM](OpenCode_Setup_NVIDIA_NIM.md) | Set up the OpenCode TUI with NVIDIA's free 100+ model catalog |
| [Gemini CLI (Windows 11)](Gemini_CLI_Setup_Windows_11.md) | Set up Google's Gemini CLI on Windows 11 — includes Antigravity CLI migration notes |

---

## Which Tool Should I Use?

| If you want… | Use |
|---|---|
| AI pair-programmer that edits files & makes git commits | **Aider** |
| A full TUI coding agent (more visual, less keyboard) | **OpenCode** |
| Google's free chatbot in a terminal | **Gemini CLI** (or Antigravity CLI after June 2026) |
| To run AI locally, no internet | **Ollama** (CLI) or **LM Studio** (GUI) |
| To call AI from your own scripts | Pick a provider from the [Free AI API Keys](Free_AI_API_Keys.md) list |

---

## What's New in 2026 (Heads-Up)

A few things changed in spring 2026 that older guides may not reflect:

- **Kimi K2.5 retired on NVIDIA NIM** (April 2026). New default coding model is **DeepSeek V4 Flash** — 1M token context, free on NVIDIA. Kimi K2.6 still exists but has high latency.
- **Google Gemini Pro models removed from the free tier** (April 1, 2026). Only Flash and Flash-Lite remain free.
- **Gemini CLI is sunsetting for free users on June 18, 2026** and being replaced by [Antigravity CLI](https://antigravity.google/download). Same Google account, free tier carries over.
- **OpenCode** moved its repo to [github.com/anomalyco/opencode](https://github.com/anomalyco/opencode) and now has 120K+ stars with 75+ provider integrations.

---

## Philosophy

This repo exists to document free or accessible AI tooling — the kind of stuff that doesn't require a credit card or a subscription to get started. Contributions, corrections, and additions welcome.

---

## Free Resources Covered

- **[NVIDIA NIM](https://build.nvidia.com)** — Free API access to 100+ frontier models (DeepSeek V4, Qwen3, Kimi K2.6, Llama 4, GLM-5.1, and more)
- **[Aider](https://aider.chat)** — Open-source AI pair-programmer, works with any OpenAI-compatible API
- **[OpenCode](https://opencode.ai)** — Open-source terminal AI coding agent with TUI, 75+ provider integrations
- **[Gemini CLI](https://github.com/google-gemini/gemini-cli)** — Google's official open-source terminal AI assistant, free with a Google account (sunsetting June 18, 2026)
- **[Antigravity CLI](https://antigravity.google)** — Google's successor to Gemini CLI, available now
- **[Groq](https://groq.com)** — Fastest free inference API (LPU hardware), no credit card
- **[Google AI Studio](https://aistudio.google.com)** — Free Gemini Flash API access with a 1M-token context window
- **[Cerebras](https://cloud.cerebras.ai)** — Free fast inference, 1M tokens/day, no credit card
- **[SambaNova](https://cloud.sambanova.ai)** — Free access to Llama 4, DeepSeek V3.1, and other large models
- **[OpenRouter](https://openrouter.ai)** — Single API key for many providers, free models available
- **[Ollama](https://ollama.com)** — Run open-source models locally, fully offline, OpenAI-compatible API
- **[LM Studio](https://lmstudio.ai)** — GUI app for local AI, built-in model browser, OpenAI-compatible API server
