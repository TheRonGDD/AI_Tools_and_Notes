# LM Studio — Local AI on Windows 11

LM Studio is a desktop application for running AI models locally. It provides a user-friendly GUI for downloading and chatting with models, and also runs a local OpenAI-compatible API server so tools like Aider and OpenCode can use your local models.

Completely free for personal use. No API key, no internet required once models are downloaded, no rate limits.

---

## 1. Prerequisites

### Hardware

LM Studio runs on CPU or GPU. GPU gives significantly better performance.

- **NVIDIA GPU:** CUDA acceleration supported on most GTX 10xx and newer cards
- **AMD GPU:** ROCm support available on Windows (better than Ollama's AMD support)
- **CPU only:** Works, but slow for larger models. 16GB+ RAM recommended for 7B models
- **RAM:** 8GB minimum. 16GB+ recommended for comfortable use

### Disk Space

Models range from ~1GB (small quantized) to 40GB+ (large full-precision). Plan accordingly.

---

## 2. Install LM Studio

1. Go to [https://lmstudio.ai](https://lmstudio.ai)
2. Click **Download for Windows**
3. Run the installer
4. Launch LM Studio from the Start menu or desktop shortcut

No PATH setup or terminal configuration needed — it's a GUI app.

---

## 3. Download a Model

LM Studio has a built-in model browser that pulls from Hugging Face.

1. Click the **Discover** tab (magnifying glass icon in the left sidebar)
2. Search for a model — e.g., `llama`, `mistral`, `phi`, `deepseek`
3. Click a result to see available quantizations
4. Click **Download** on your preferred version

### Choosing a quantization

Models come in different quantized sizes. Higher Q = better quality but more RAM required.

| Format | Quality | RAM use | Recommended for |
|---|---|---|---|
| Q2_K | Low | Minimal | Very low RAM only |
| Q4_K_M | Good | Moderate | Best balance — most users start here |
| Q5_K_M | Better | Higher | If you have extra VRAM/RAM |
| Q8_0 | Near-original | High | High-end GPUs with lots of VRAM |

**Rule of thumb:** Start with **Q4_K_M** of your chosen model.

### Recommended models to start with

| Model | Size (Q4) | Best for |
|---|---|---|
| Llama 3.2 3B | ~2GB | Fast, everyday tasks |
| Llama 3.1 8B | ~5GB | Good all-rounder |
| Mistral 7B | ~4GB | Coding and reasoning |
| Phi-3 Mini | ~2GB | Fast, good coding ability |
| DeepSeek R1 7B | ~5GB | Reasoning with thinking mode |
| Qwen 2.5 Coder 7B | ~5GB | Code-focused tasks |
| Gemma 2 2B | ~2GB | Fast, lightweight |

---

## 4. Chat in the GUI

1. Click the **Chat** tab (speech bubble icon)
2. Click **Select a model to load** at the top and choose your downloaded model
3. Wait for it to load (first load takes a moment)
4. Type in the chat box and press Enter

The chat interface supports system prompts, conversation history, and parameter controls (temperature, context length, etc.) via the right-hand panel.

---

## 5. Using LM Studio as an API (OpenAI-Compatible)

LM Studio runs a local OpenAI-compatible server. Enable it to use your local models with external tools.

### Start the server

1. Click the **Developer** tab (the `<->` icon in the left sidebar)
2. Click **Start Server**
3. The server runs on `http://localhost:1234` by default

**Base URL:** `http://localhost:1234/v1`
**API Key:** Any string works (e.g. `lm-studio`) — not validated

### Use with Aider

```powershell
$env:OPENAI_API_BASE = "http://localhost:1234/v1"
$env:OPENAI_API_KEY = "lm-studio"
aider --model openai/<your-model-name>
```

Replace `<your-model-name>` with the model identifier shown in LM Studio's Developer tab.

### Use with OpenCode

Add a provider to `~/.config/opencode/opencode.json`:

```json
{
  "provider": {
    "lmstudio": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "LM Studio (Local)",
      "options": {
        "baseURL": "http://localhost:1234/v1",
        "apiKey": "lm-studio"
      },
      "models": {
        "your-model-name": {
          "name": "My Local Model"
        }
      }
    }
  }
}
```

### Test the API directly

```powershell
$headers = @{
    "Authorization" = "Bearer lm-studio"
    "Content-Type"  = "application/json"
}
$body = @{
    model    = "your-model-name"
    messages = @(@{ role = "user"; content = "Say hello in one sentence." })
} | ConvertTo-Json -Depth 3

Invoke-RestMethod -Uri "http://localhost:1234/v1/chat/completions" -Method Post -Headers $headers -Body $body
```

---

## 6. LM Studio CLI (lms)

LM Studio also ships with a CLI tool called `lms` for managing models and the server from the terminal.

```powershell
# Check version
lms version

# List downloaded models
lms ls

# Load a model
lms load <model-name>

# Start the API server
lms server start

# Stop the server
lms server stop

# Get server status
lms status
```

> `lms` is available after installing LM Studio. If it is not recognized, restart your terminal.

---

## 7. LM Studio vs Ollama

Both are free local AI tools. Here is when to choose one over the other:

| | LM Studio | Ollama |
|---|---|---|
| Interface | Full desktop GUI + CLI | Desktop GUI (system tray) + CLI |
| Model format | GGUF (Hugging Face) | Ollama library + GGUF import |
| Model browser | Built-in GUI browser | Command line pull |
| API server | Yes (port 1234) | Yes (port 11434) |
| AMD GPU support | Better | Limited on Windows |
| Beginner friendly | Easier — visual interface | Slightly more technical |
| Custom models | Easy GGUF import | Requires Modelfile |
| Headless/server use | Possible via CLI | Better suited |

**Use LM Studio** if you want a visual interface, prefer browsing models in a GUI, or are on AMD.
**Use Ollama** if you prefer pure CLI, want simpler automation, or are running headless.

Both can run simultaneously on different ports.

---

## Quick Start Summary

```
1. Download LM Studio from https://lmstudio.ai and install it

2. Open LM Studio → Discover tab → search for a model → download Q4_K_M

3. Chat tab → select your model → start chatting

4. (Optional) Developer tab → Start Server
   Then use from Aider/OpenCode at http://localhost:1234/v1
```

---

## Troubleshooting

| Problem | Solution |
|---|---|
| Model loads slowly | Normal for first load — subsequent loads are faster |
| Out of memory / crash on load | Switch to a smaller model or lower quantization (Q4 → Q3 or Q2) |
| GPU not being used | Check Developer tab → GPU Offload settings. Set layers to max |
| API server not responding | Make sure you clicked Start Server in the Developer tab |
| `lms` not recognized | Restart terminal after installing LM Studio |
| Model not showing in API | Model must be loaded in LM Studio before it appears via API |
| Slow responses on CPU | Expected — use a 3B or smaller model for CPU-only setups |

---

## References

- [LM Studio Official Site](https://lmstudio.ai)
- [LM Studio Documentation](https://lmstudio.ai/docs)
- [LM Studio GitHub](https://github.com/lmstudio-ai)
- [GGUF Models on Hugging Face](https://huggingface.co/models?library=gguf)
