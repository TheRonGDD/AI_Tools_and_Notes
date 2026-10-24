# Ollama — Local AI on Windows 11

Ollama lets you run AI models locally on your own machine — no API key, no internet connection required, no rate limits, and completely private. It also runs a local OpenAI-compatible API server, so tools like Aider and OpenCode work with it out of the box.

**Runs natively on Windows 11.** No WSL required.

---

## 1. Prerequisites

### GPU (Optional but Recommended)

Ollama runs on CPU or GPU. GPU is significantly faster.

- **NVIDIA:** CUDA 11.3+ supported. Most GTX 10xx and newer cards work.
- **AMD:** ROCm support on Windows is limited — CPU fallback is used on most AMD setups.
- **CPU only:** Works fine, just slower. 8GB+ RAM recommended.

**Check your GPU:**

```powershell
nvidia-smi
```

If that returns GPU info, Ollama will use it automatically.

---

## 2. Install Ollama

1. Go to [https://ollama.com/download](https://ollama.com/download)
2. Click **Download for Windows**
3. Run the installer — it installs Ollama and starts it as a background service automatically
4. Close and reopen your terminal, then verify:

```powershell
ollama --version
```

> Ollama runs as a system tray app and background service. It starts automatically with Windows. You do not need to manually start it before running commands.

> **Current version as of this writing:** 0.40.11 (released 2026-10-24), up from 0.34.2 a month ago. That is 17 releases in five weeks. Ollama updates itself; `ollama --version` shows what you have.

> **What changed since 0.34.2, in order of how likely it is to affect you:**
>
> - **⚠️ Your existing models get re-converted in the background, and temporarily take double the disk** (0.40.2, 2026-10-08). From 0.40 on, models downloaded with older versions are upgraded to run on llama.cpp the first time you run them, and Ollama **keeps the original copy as a backup** so a downgrade stays safe. Ollama says a future release will delete those backups automatically; until then, if you have a large library, watch your free space. The 0.40.2 release notes include a `jq` one-liner to remove the backups by hand. After that, downgrading below 0.40 means re-pulling.
> - **The account step is gone from CLI onboarding** (0.40.1). 0.34.2 had added a sign-in prompt on first run; that has been removed again. Nothing in this guide ever needed an account, and now Ollama does not ask.
> - **New coding model worth trying locally: Flint** (0.40.8, Windows support in 0.40.9). Recursive's Apache 2.0 40B mixture-of-experts with only ~2.5B active per token, 262K context, built for coding agents. **Vendor-reported** scores are 74.2% SWE-bench Verified and 54.9% Terminal-Bench 2.1, not independently verified here. It is a 22.9GB download. Also free on OpenRouter as `recursive/flint-v1.0:free`.
> - **NVIDIA Nemotron 3.5 Super and Bolt** (0.40.5, 0.40.10). Super is a 120B / 12B-active vision-language model with up to 1M context (90.2GB). **Bolt** distills it to 74B / 9B active, tuned for a 24GB NVIDIA card (24.8GB), and **Bolt E4B** is a small Gemma 4-based vision model at 7.9GB. Both Bolts ship with a draft model for speculative decoding.
> - **Decision models** (0.35.0, 0.35.1). A new category, served on a new `/v1/systemone` endpoint, that returns choices, probabilities and scores instead of text. Good for ticket triage, routing and classification. Available: Nimble (Bespoke Labs), Tev1 (Together AI), Cloudflare's Clef and Clef Flash, Perplexity's `pplx-decider`, and `d3` from vLLM's Semantic Router. From 0.40.4 you can call them from the terminal with `ollama run <model> --ask "question"`.
> - **Apple Silicon runs on MLX by default** (0.40.0). Not relevant on Windows, but it explains a lot of the changelog.
> - **Speech output** (0.40.11) via `/v1/audio/speech`, but **macOS only** for now.
> - **Smaller fixes that matter on Windows:** orphaned runner processes are prevented (0.40.6), manifests no longer use symlinks (0.40.1), and streaming OpenAI Responses and Anthropic Messages requests now report a generation failure instead of ending silently after partial output (0.40.9).
> - **Still in force from September:** since 0.34.1, `ollama create` no longer converts and quantizes safetensors into GGUF for you. Use llama.cpp tooling for that step.

> **Qwen 3.8 27B, and the model ID people get wrong.** Locally it is `qwen3.8:27b` (17.7GB; plain `qwen3.8` pulls the same thing). On **Groq's free API** it is `qwen/qwen3.8-27b`, with a hyphen where Ollama uses a colon, and the previous `qwen/qwen3.6-27b` ID was shut down on Groq on 2026-09-14. Context differs by host: Ollama's library lists 256K, Groq serves 131,072. **OpenRouter's free endpoint for it is gone** as of 2026-10-24, so for a free hosted copy, Groq is now the only option, at about 0.2 seconds per call. There is no `qwen3.8:9b` or `:4b` tag; the 3.5 family is still the small option.

### System Tray GUI

After installing, Ollama appears in the Windows system tray (bottom-right corner of the taskbar). Right-clicking the icon gives you quick access to:

- View and manage running models
- Restart or stop the Ollama service
- Open the model library in your browser
- Check for updates

The GUI includes a built-in chat interface as well as model management. You can chat directly from the app without opening a terminal.

---

## 3. Download and Run a Model

### Pull a model

```powershell
ollama pull llama3.2
```

Ollama downloads the model to your local machine. Downloads are cached — you only download once.

### Run it interactively

```powershell
ollama run llama3.2
```

This opens a chat session directly in the terminal. Type your message and press Enter. Type `/bye` to exit.

### List downloaded models

```powershell
ollama list
```

### Remove a model

```powershell
ollama rm llama3.2
```

---

## 4. Recommended Free Models (October 2026)

Sizes below are actual download sizes read from the Ollama registry manifests on 2026-10-24, and every tag was verified to exist on that date. Several sizes moved slightly since August, as models were repackaged for the 0.40 llama.cpp runner.

| Model | Pull command | Size | Best for |
|---|---|---|---|
| Qwen 3.5 0.8B | `ollama pull qwen3.5:0.8b` | 1.3GB | Very fast, minimal hardware |
| Llama 3.2 3B | `ollama pull llama3.2` | 2.0GB | Fast everyday tasks, low RAM |
| Qwen 3.5 4B | `ollama pull qwen3.5:4b` | 3.3GB | Best tiny generalist right now |
| DeepSeek R1 8B | `ollama pull deepseek-r1:8b` | 5.2GB | Reasoning model with visible thinking |
| Qwen 3.5 9B | `ollama pull qwen3.5:9b` | 6.6GB | Strong all-around coding/reasoning at single-GPU size |
| **Nemotron 3.5 Bolt E4B** | `ollama pull nemotron-3.5-bolt:e4b` | 7.9GB | **New.** Small vision + tool-use model, 128K context, speculative decoding built in |
| Gemma 4 12B | `ollama pull gemma4:12b` | 8.0GB | Google's 2026 multimodal flagship, smallest size |
| GPT-OSS 20B | `ollama pull gpt-oss:20b` | 13.8GB | OpenAI open weights, MXFP4, 128K context. Great 16GB pick |
| Devstral Small 2 24B | `ollama pull devstral-small-2` | 15.2GB | Agentic coding, with vision + tools |
| Qwen 3.8 27B | `ollama pull qwen3.8:27b` | 17.7GB | Best mid-range generalist, 256K context. Also on Groq's free API |
| Qwen 3.6 27B | `ollama pull qwen3.6:27b` | 17.8GB | Previous generation; superseded by 3.8 above |
| Muse Glimmer 30B | `ollama pull muse-glimmer` | 18.2GB | Meta's agent-harness model, also free on NVIDIA NIM |
| Qwen 3 Coder 30B | `ollama pull qwen3-coder` | 18.6GB | Code specialist, 256K context. Still the safest local coding pick |
| Gemma 4 26B | `ollama pull gemma4:26b` | 18.7GB | Frontier-ish quality on a 24GB card |
| Gemma 4 31B | `ollama pull gemma4:31b` | 20.4GB | Largest Gemma 4, needs a 24GB card |
| Qwen 3.5 35B / Qwen 3.6 35B | `ollama pull qwen3.5:35b` / `qwen3.6:35b` | 22.1GB / 22.6GB | Top of the single-24GB-card range, tight fit |
| **Flint 40B** | `ollama pull flint` | 22.9GB | **New. Most promising local coder this cycle**: only ~2.5B active, so it runs fast for its size. 262K context |
| **Nemotron 3.5 Bolt** | `ollama pull nemotron-3.5-bolt` | 24.8GB | **New.** 74B / 9B active, tuned for a 24GB NVIDIA card |
| Nemotron 3.5 Lightning | `ollama pull nemotron-3.5-lightning` | 25.4GB | Low-latency agent model, also free on NVIDIA NIM |
| Devstral 2 123B | `ollama pull devstral-2` | 74.9GB | Large agentic coder, multi-GPU territory |
| Qwen 3.5 122B | `ollama pull qwen3.5:122b` | 81.4GB | Largest Qwen 3.5, multi-GPU territory |
| **Nemotron 3.5 Super** | `ollama pull nemotron-3.5-super` | 90.2GB | **New.** 120B / 12B-active vision model, up to 1M context. Multi-GPU or large unified memory |

> **Not sure what fits your hardware?** A rough guide at Q4 quantization:
> - 8GB RAM/VRAM → 4B or smaller
> - 16GB RAM/VRAM → 9B–20B (`gpt-oss:20b` is the standout here)
> - 24GB+ VRAM → 24B–31B
> - 48GB+ VRAM → 70B and up
>
> **For local coding specifically:** `qwen3-coder:30b` is still the safe pick for raw code quality. `devstral-small-2` is the pick for agentic workflows (the read, reason, edit, verify loop), at 46.8% SWE-Bench Verified in 15GB. **Flint is the one to try this month**: Recursive reports 74.2% SWE-bench Verified for a 22.9GB download with only ~2.5B parameters active per token, which would put it far ahead of Devstral Small 2 at a similar size. That is a vendor-reported number from its release, so treat it as a claim until independent results show up, but it is cheap to test.
>
> **Note on the very large models:** GLM-5.3 leads the open-weight models this repo tracks, but it is a very large MoE that needs multi-GPU servers or a very large unified-memory machine. On normal desktop hardware, run it through [NVIDIA NIM](Free_AI_API_Keys.md#nvidia-nim) for free instead of locally. (As of 2026-10-24, `z-ai/glm-5.3` answers in 7 to 27 seconds on NIM, and the new `minimaxai/minimax-m3.1-flash` in under a second. Kimi K3, which this note also mentioned in September, reached end of life on NIM on 2026-10-15.)

Browse all available models at [https://ollama.com/library](https://ollama.com/library).

---

## 5. Using Ollama as an API (OpenAI-Compatible)

Ollama runs a local REST server on port **11434** that is OpenAI-compatible. This means any tool that supports a custom base URL can use your local models.

**Base URL:** `http://localhost:11434/v1`
**API Key:** Any string works (e.g. `ollama`) — Ollama doesn't validate it but some tools require a non-empty value

### Use with Aider

```powershell
$env:OPENAI_API_BASE = "http://localhost:11434/v1"
$env:OPENAI_API_KEY = "ollama"
aider --model openai/llama3.2
```

### Use with OpenCode

Add a provider to `~/.config/opencode/opencode.json` (use that filename, not `config.json`, which OpenCode 2 ignores):

```json
{
  "provider": {
    "ollama": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama (Local)",
      "options": {
        "baseURL": "http://localhost:11434/v1",
        "apiKey": "ollama"
      },
      "models": {
        "llama3.2": {
          "name": "Llama 3.2"
        }
      }
    }
  }
}
```

### Test the API directly

```powershell
$headers = @{ "Content-Type" = "application/json" }
$body = @{
    model    = "llama3.2"
    messages = @(@{ role = "user"; content = "Say hello in one sentence." })
} | ConvertTo-Json -Depth 3

Invoke-RestMethod -Uri "http://localhost:11434/v1/chat/completions" -Method Post -Headers $headers -Body $body
```

---

## 6. Useful Commands

| Command | What it does |
|---|---|
| `ollama list` | List all downloaded models |
| `ollama pull <model>` | Download a model |
| `ollama run <model>` | Start an interactive chat session |
| `ollama rm <model>` | Delete a model |
| `ollama ps` | Show currently running models |
| `ollama show <model>` | Show model details and parameters |
| `ollama serve` | Manually start the Ollama server (if not running) |

---

## 7. Windows Terminal Profile (Optional)

Add this to your Windows Terminal `settings.json` to open an Ollama chat directly from the dropdown:

```json
{
    "guid": "{a1b2c3d4-5678-90ab-cdef-555555555555}",
    "hidden": false,
    "name": "Ollama",
    "commandline": "C:\\Program Files\\Git\\bin\\bash.exe --login -i -c 'ollama run llama3.2; exec bash'",
    "icon": "https://ollama.com/public/ollama.png"
}
```

Change `llama3.2` to whichever model you want to open by default.

---

## Quick Start Summary

```powershell
# 1. Download and install Ollama from https://ollama.com/download

# 2. Pull a model
ollama pull llama3.2

# 3. Chat with it
ollama run llama3.2

# 4. (Optional) Use with Aider or OpenCode via localhost API
# Base URL: http://localhost:11434/v1
# API Key:  ollama
```

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `ollama` not recognized | Restart terminal. If still broken, reinstall Ollama |
| Model runs slowly | Normal on CPU — try a smaller model (1B or 3B) |
| Out of memory error | Switch to a smaller model. Close other apps to free RAM |
| Port 11434 not responding | Run `ollama serve` to start the server manually |
| GPU not being used | Verify NVIDIA drivers are up to date and `nvidia-smi` works |
| Download stuck or slow | Large models (7B+) are several GB — give it time |

---

## Ollama vs LM Studio

Both are free local AI tools. Here is when to choose one over the other:

| | Ollama | LM Studio |
|---|---|---|
| Interface | System tray GUI + CLI | Full desktop GUI + CLI |
| Chat UI | GUI chat + terminal (`ollama run`) | Built-in GUI chat interface |
| Model browser | CLI (`ollama pull`) + web library | Built-in GUI browser |
| API server | Yes (port 11434) | Yes (port 1234) |
| AMD GPU support | Limited on Windows | Better |
| Beginner friendly | Slightly more technical | Easier — visual interface |
| Custom models | Requires Modelfile | Easy GGUF drag-and-drop |
| Headless/server use | Better suited | Possible via `lms` CLI |
| Automation/scripting | Easier | Possible but less common |

**Use Ollama** if you prefer CLI workflows, want simpler automation, or are running on a server or headless machine.
**Use LM Studio** if you want a full visual interface, prefer browsing and managing models in a GUI, or are on AMD.

Both can run simultaneously on different ports and be used with the same tools (Aider, OpenCode, etc.).

---

## References

- [Ollama Official Site](https://ollama.com)
- [Ollama Model Library](https://ollama.com/library)
- [Ollama GitHub](https://github.com/ollama/ollama)
- [Ollama API Documentation](https://github.com/ollama/ollama/blob/main/docs/api.md)
- [Ollama OpenAI Compatibility](https://github.com/ollama/ollama/blob/main/docs/openai.md)
