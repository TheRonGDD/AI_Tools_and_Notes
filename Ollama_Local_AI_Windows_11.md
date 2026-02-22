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

### System Tray GUI

After installing, Ollama appears in the Windows system tray (bottom-right corner of the taskbar). Right-clicking the icon gives you quick access to:

- View and manage running models
- Restart or stop the Ollama service
- Open the model library in your browser
- Check for updates

This is a lightweight management GUI — it does not include a chat interface. For a full chat UI, use `ollama run <model>` in the terminal, or pair Ollama with a front-end like Open WebUI (see references).

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

## 4. Recommended Free Models

| Model | Pull command | Size | Best for |
|---|---|---|---|
| Llama 3.2 3B | `ollama pull llama3.2` | ~2GB | Fast, everyday tasks, low RAM |
| Llama 3.2 1B | `ollama pull llama3.2:1b` | ~1GB | Very fast, minimal hardware |
| Llama 3.1 8B | `ollama pull llama3.1` | ~5GB | Good balance of speed and quality |
| Llama 3.1 70B | `ollama pull llama3.1:70b` | ~40GB | High quality, needs strong GPU |
| Mistral 7B | `ollama pull mistral` | ~4GB | Good coding and reasoning |
| Phi-3 Mini | `ollama pull phi3` | ~2GB | Fast, Microsoft model, good for coding |
| Gemma 2 2B | `ollama pull gemma2:2b` | ~2GB | Fast, Google model |
| DeepSeek R1 7B | `ollama pull deepseek-r1` | ~5GB | Reasoning model with thinking mode |
| Qwen 2.5 Coder | `ollama pull qwen2.5-coder` | ~4GB | Specialized for code |
| CodeLlama | `ollama pull codellama` | ~4GB | Code generation and completion |

> **Not sure what fits your hardware?** A rough guide: 8GB RAM → stick to 3B or smaller. 16GB RAM → 7B/8B models run comfortably. 32GB+ RAM or a GPU with 8GB+ VRAM → 13B+ models.

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

Add a provider to `~/.config/opencode/opencode.json`:

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
| Chat UI | Terminal (`ollama run`) | Built-in GUI chat interface |
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
