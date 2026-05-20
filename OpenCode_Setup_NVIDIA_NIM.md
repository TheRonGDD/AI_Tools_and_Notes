# OpenCode Setup with NVIDIA NIM Free API (Windows 11)

OpenCode is an open-source AI coding agent with a rich TUI (terminal user interface). It supports any OpenAI-compatible API — including NVIDIA's free NIM (NVIDIA Inference Microservices) endpoints, which host 100+ frontier models for free.

**OpenCode runs natively on Windows 11** via Scoop, Chocolatey, or the universal installer. WSL is not required.

> **Recommended model (May 2026):** `deepseek-ai/deepseek-v4-flash` — DeepSeek's 284B MoE coding model with a 1M-token context window. Free on NVIDIA NIM, optimized for coding and agentic tasks. The Kimi K2.5 model previously featured in this guide was retired in April 2026; the configuration steps below work the same for any NVIDIA-hosted model — just swap the model ID.

---

## 1. Prerequisites

### A) Git

OpenCode uses Git for project context. Most workflows require it.

**Check if you already have it:**

```powershell
git --version
```

If not installed, download from [https://git-scm.com/download/win](https://git-scm.com/download/win) and use all default options.

### B) Node.js (only if installing via npm)

If you plan to install via npm, you need Node.js v18 or later:

1. Go to [https://nodejs.org](https://nodejs.org)
2. Download the **LTS** version
3. Run the installer with all default options
4. Close and reopen your terminal

Skip this if you install via Scoop, Chocolatey, or the binary download.

---

## 2. Get Your Free NVIDIA API Key

1. Go to [https://build.nvidia.com](https://build.nvidia.com)
2. Sign in or create a free NVIDIA Developer account (just an email — no credit card)
3. Pick any model — for example [DeepSeek V4 Flash](https://build.nvidia.com/deepseek-ai/deepseek-v4-flash)
4. Click **"Get API Key"** (or "Build with this NIM")
5. Copy the key — it starts with `nvapi-`

A single `nvapi-` key works for all 100+ models on NVIDIA's free tier.

---

## 3. Install OpenCode

Pick one of these install methods:

### Option A: Scoop (recommended for Windows)

If you have [Scoop](https://scoop.sh):

```powershell
scoop install opencode
```

### Option B: Chocolatey

```powershell
choco install opencode
```

### Option C: npm (if you already have Node.js)

```powershell
npm install -g opencode-ai
```

### Option D: Universal installer (bash, works in Git Bash on Windows)

```bash
curl -fsSL https://opencode.ai/install | bash
```

### Option E: Download the binary

Go to the [GitHub releases page](https://github.com/anomalyco/opencode/releases) and download the Windows binary. Place it somewhere in your PATH.

**Verify it installed:**

```powershell
opencode --version
```

> **"opencode is not recognized" error:** Close and reopen your terminal. If using npm and it still fails, run `npm config get prefix` and add its `\bin` folder to your system PATH.

---

## 4. Configure NVIDIA NIM as a Provider

OpenCode looks for its config file at:

```
C:\Users\YOUR_USERNAME\.config\opencode\opencode.json
```

You can name it `opencode.jsonc` instead if you want to add comments.

Create or edit that file with the following content, replacing the API key with yours:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "nvidia": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "NVIDIA NIM",
      "options": {
        "baseURL": "https://integrate.api.nvidia.com/v1",
        "apiKey": "nvapi-YOUR_KEY_HERE"
      },
      "models": {
        "deepseek-ai/deepseek-v4-flash": {
          "name": "DeepSeek V4 Flash",
          "limit": {
            "context": 1000000,
            "output": 32768
          }
        },
        "deepseek-ai/deepseek-v4-pro": {
          "name": "DeepSeek V4 Pro",
          "limit": {
            "context": 1000000,
            "output": 32768
          }
        },
        "moonshotai/kimi-k2.6": {
          "name": "Kimi K2.6",
          "limit": {
            "context": 262144,
            "output": 32768
          }
        },
        "qwen/qwen3-coder-480b-a35b-instruct": {
          "name": "Qwen3 Coder 480B",
          "limit": {
            "context": 262144,
            "output": 32768
          }
        }
      }
    }
  }
}
```

### Setting a default model

Add a `model` field at the top level so OpenCode picks it without prompting:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "nvidia/deepseek-ai/deepseek-v4-flash",
  "provider": {
    ...
  }
}
```

The format is `<provider-key>/<model-id>` — here, `nvidia/` is the provider key from your config, and the rest is the NVIDIA model ID.

---

## 5. Launch OpenCode

Navigate to any project folder and run:

```powershell
cd C:\path\to\your\project
opencode
```

OpenCode opens a full TUI in the terminal. Your project files and git history are used as context automatically.

---

## 6. Selecting a Model Inside OpenCode

If you didn't set a default model, select it interactively:

1. Press `/` to open the command palette
2. Type `models` and press Enter
3. Find **NVIDIA NIM → DeepSeek V4 Flash** (or whichever model) and select it

Or type `/models` directly in the chat input.

---

## 7. Picking a Model

NVIDIA NIM hosts 100+ free models. Browse the full catalog at [https://build.nvidia.com/models](https://build.nvidia.com/models). Some good picks for coding (May 2026):

| Model ID | Best for |
|---|---|
| `deepseek-ai/deepseek-v4-flash` | Default recommendation — fast, 1M context, strong coding |
| `deepseek-ai/deepseek-v4-pro` | Higher quality on hard problems, slower (1.6T MoE) |
| `moonshotai/kimi-k2.6` | Long-horizon agentic coding (256K context) — but slow latency |
| `qwen/qwen3-coder-480b-a35b-instruct` | Code-specialist Qwen3 |
| `nvidia/llama-3.3-nemotron-super-49b` | NVIDIA-tuned Llama 3.3 |
| `meta/llama-4-maverick-17b-128e-instruct` | Meta's latest |

To add more models, just add entries to the `models` map in `opencode.json` and restart OpenCode.

> Model IDs change over time. The authoritative list is the URL slug on the model card page on build.nvidia.com.

---

## 8. Essential OpenCode Commands

Type these in the chat input while inside an OpenCode session:

| Command | What it does |
|---|---|
| `/models` | Browse and switch AI models |
| `/connect` | Add a new provider interactively |
| `/agents` | Switch between build and plan agents |
| `/theme` | Change the color theme |
| `/help` | Show all available commands |
| `Ctrl+C` | Cancel current generation |
| `Ctrl+D` | Exit OpenCode |

### Build vs Plan agents

OpenCode ships with two built-in agents:

- **build** — full development access, edits files, runs commands
- **plan** — read-only, can analyze and propose changes but not modify the filesystem

Switch between them with `/agents`. Use plan when you want to think through an approach before letting the model touch your code.

### Basic workflow example

```
> Look at src/main.py and add error handling to the parse_config function
> Now write a test for that function
> Run the tests and fix any failures
```

OpenCode edits files directly. Review changes in your editor or with `git diff`.

---

## 9. Tips

### Free tier rate limits
NVIDIA's free tier is roughly **40 requests per minute** across all models, but limits vary by model. If you get a 429 error, wait a minute and retry.

### Context window
DeepSeek V4 Flash and Pro both support **1,000,000 tokens** of context — enough for most full repos. OpenCode automatically includes relevant files.

### Keep your API key out of git
Your API key is stored in plain text in `opencode.json`. The default location (`~/.config/opencode/`) is outside any project, but double-check that you haven't accidentally checked in a copy.

### MCP support
OpenCode supports MCP (Model Context Protocol) servers — same protocol used by Claude Desktop. Configure them under a `mcp` key in `opencode.json`. See the OpenCode docs for examples.

---

## 10. Verify Everything Works

### Test your API key directly (before opening OpenCode)

Paste this into PowerShell to confirm your NVIDIA key is working:

```powershell
$headers = @{
    "Authorization" = "Bearer nvapi-YOUR_KEY_HERE"
    "Content-Type"  = "application/json"
}
$body = @{
    model      = "deepseek-ai/deepseek-v4-flash"
    messages   = @(@{ role = "user"; content = "Say hello in one sentence." })
    max_tokens = 50
} | ConvertTo-Json -Depth 3

Invoke-RestMethod -Uri "https://integrate.api.nvidia.com/v1/chat/completions" -Method Post -Headers $headers -Body $body
```

If you get a response with a `choices` array containing a message, your key is working.

### Test OpenCode end-to-end

```powershell
cd C:\path\to\your\project
opencode
```

Then type: `What files are in this directory?` and press Enter. If OpenCode responds with a list of files, everything is working.

---

## 11. Windows Terminal Profile (Optional)

To open OpenCode directly from the Windows Terminal dropdown, add this profile to your `settings.json` (`Ctrl+,` → open JSON file):

```json
{
    "guid": "{a1b2c3d4-5678-90ab-cdef-333333333333}",
    "hidden": false,
    "name": "OpenCode",
    "commandline": "C:\\Program Files\\Git\\bin\\bash.exe --login -i -c 'cd /j/AI_Files && opencode; exec bash'",
    "startingDirectory": "J:\\AI_Files"
}
```

Add it inside the `profiles.list` array in settings.json.

---

## Quick Start Summary

```powershell
# 1. Install OpenCode (pick one)
scoop install opencode                # if you use Scoop
choco install opencode                # if you use Chocolatey
npm install -g opencode-ai            # if you have Node.js
# OR download binary from https://github.com/anomalyco/opencode/releases

# 2. Create config file at C:\Users\YOUR_USERNAME\.config\opencode\opencode.json
#    (see Section 4 above for the full contents)

# 3. Launch OpenCode in your project directory
cd C:\path\to\your\project
opencode
```

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `opencode is not recognized` | Close/reopen terminal. If using npm install, add npm global bin to PATH: run `npm config get prefix` and add its `\bin` folder to system PATH. If using binary, ensure it's in a PATH directory |
| `/models` doesn't show NVIDIA NIM | Check that `opencode.json` is valid JSON (no trailing commas, correct path) |
| API returns 401 Unauthorized | Double-check your `nvapi-` key in opencode.json |
| API returns 404 Not Found | Verify the model ID matches a current model on build.nvidia.com |
| API returns 410 Gone | Model has been deprecated — pick a different one |
| API returns 429 Too Many Requests | Hit free tier rate limit (~40 RPM) — wait 60 seconds and retry |
| Slow responses | Normal for reasoning models. Try DeepSeek V4 Flash for faster responses |
| Config changes not picked up | Restart OpenCode after editing opencode.json |

---

## References

- [OpenCode Official Site](https://opencode.ai)
- [OpenCode GitHub](https://github.com/anomalyco/opencode)
- [OpenCode Download / Install](https://opencode.ai/docs/install/)
- [OpenCode Docs: Providers](https://opencode.ai/docs/providers/)
- [OpenCode Docs: Config](https://opencode.ai/docs/config/)
- [OpenCode Docs: Models](https://opencode.ai/docs/models/)
- [NVIDIA NIM Model Catalog](https://build.nvidia.com/models)
- [DeepSeek V4 Flash on NVIDIA NIM](https://build.nvidia.com/deepseek-ai/deepseek-v4-flash)
- [NVIDIA NIM API Reference](https://docs.api.nvidia.com/nim/reference)
