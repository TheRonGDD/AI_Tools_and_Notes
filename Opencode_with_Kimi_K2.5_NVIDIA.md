# OpenCode Setup with Kimi K2.5 via NVIDIA Free API (Windows 11)

OpenCode is an open-source AI coding agent with a rich TUI (terminal user interface). It supports any OpenAI-compatible API — including NVIDIA's free Kimi K2.5 endpoint.

**OpenCode runs on Windows 11.** For the best experience, the developers recommend using WSL (Windows Subsystem for Linux). Native Windows support is improving — npm install works natively, and you can also download the binary directly.

---

## 1. Prerequisites

### A) Git

OpenCode uses Git for project context. Most workflows require it.

**Check if you already have it:**

```powershell
git --version
```

If not installed, download from [https://git-scm.com/download/win](https://git-scm.com/download/win) and use all default options.

### B) Node.js (only needed for npm install method)

If you plan to install via npm, you need Node.js v18 or later:

1. Go to [https://nodejs.org](https://nodejs.org)
2. Download the **LTS** version
3. Run the installer with all default options
4. Close and reopen your terminal

If you install via binary download instead, Node.js is not required.

---

## 2. Get Your Free NVIDIA API Key

1. Go to [https://build.nvidia.com/moonshotai/kimi-k2.5](https://build.nvidia.com/moonshotai/kimi-k2.5)
2. Sign in or create a free NVIDIA Developer account
3. Click **"Get API Key"** (or "Build with this NIM")
4. Copy the key — it starts with `nvapi-`

Keep this key handy for the configuration step below.

---

## 3. Install OpenCode

OpenCode is a Go binary with multiple install methods. Pick one:

### Option A: npm (if you already have Node.js)

```powershell
npm install -g opencode-ai
```

### Option B: Download the binary (no Node.js needed)

Go to the [GitHub releases page](https://github.com/opencode-ai/opencode/releases) and download the Windows binary. Place it somewhere in your PATH.

### Option C: WSL (recommended by OpenCode for best Windows experience)

If you have WSL set up:

```bash
curl -fsSL https://opencode.ai/install | bash
```

**Verify it installed:**

```powershell
opencode --version
```

> **"opencode is not recognized" error:** Close and reopen your terminal. If using npm and it still fails, run `npm config get prefix` and add its `\bin` folder to your system PATH.

---

## 4. Configure Kimi K2.5 as a Provider

OpenCode uses a config file at:

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
        "moonshotai/kimi-k2.5": {
          "name": "Kimi K2.5",
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

### Setting Kimi K2.5 as the default model

Add a `model` field at the top level to make it your default:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "nvidia/moonshotai/kimi-k2.5",
  "provider": {
    ...
  }
}
```

---

## 5. Launch OpenCode

Navigate to any project folder and run:

```powershell
cd C:\path\to\your\project
opencode
```

OpenCode opens a full TUI in the terminal. Your project files and git history are used as context automatically.

---

## 6. Selecting Kimi K2.5 Inside OpenCode

If you didn't set a default model, select it interactively:

1. Press `/` to open the command palette
2. Type `models` and press Enter
3. Find **NVIDIA NIM → Kimi K2.5** and select it

Or type `/models` directly in the chat input.

---

## 7. Essential OpenCode Commands

Type these in the chat input while inside an OpenCode session:

| Command | What it does |
|---|---|
| `/models` | Browse and switch AI models |
| `/connect` | Add a new provider interactively |
| `/theme` | Change the color theme |
| `/help` | Show all available commands |
| `Ctrl+C` | Cancel current generation |
| `Ctrl+D` | Exit OpenCode |

### Basic workflow example:

```
> Look at src/main.py and add error handling to the parse_config function
> Now write a test for that function
> Run the tests and fix any failures
```

OpenCode edits files directly. Review changes in your editor or with `git diff`.

---

## 8. Tips for Kimi K2.5

### Context window
Kimi K2.5 supports **262,144 tokens** of context — more than enough for large repos. OpenCode automatically includes relevant files.

### Thinking mode
Kimi K2.5 uses chain-of-thought reasoning by default. Responses may take a few seconds longer than lightweight models but produce significantly better code.

### Free tier limits
The NVIDIA Developer Program provides free API access with rate limits. If you get a 429 error, wait a minute and retry. Check current limits at the [model card page](https://build.nvidia.com/moonshotai/kimi-k2.5/modelcard).

### Keep your API key out of git
Your API key is stored in plain text in `opencode.json`. Make sure `~/.config/opencode/` is not inside a git repository, or add it to your global `.gitignore`.

---

## 9. Verify Everything Works

### Test your API key directly (before opening OpenCode)

Paste this into PowerShell to confirm your NVIDIA key is working:

```powershell
$headers = @{
    "Authorization" = "Bearer nvapi-YOUR_KEY_HERE"
    "Content-Type"  = "application/json"
}
$body = @{
    model      = "moonshotai/kimi-k2.5"
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

## 10. Windows Terminal Profile (Optional)

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
npm install -g opencode-ai          # if you have Node.js
# OR download binary from https://github.com/opencode-ai/opencode/releases

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
| API returns 404 Not Found | Verify the model ID is exactly `moonshotai/kimi-k2.5` |
| API returns 429 Too Many Requests | Hit free tier rate limit — wait 60 seconds and retry |
| Slow responses | Normal for thinking mode — Kimi K2.5 reasons before responding |
| Config changes not picked up | Restart OpenCode after editing opencode.json |

---

## References

- [OpenCode Official Site](https://opencode.ai)
- [OpenCode GitHub](https://github.com/opencode-ai/opencode)
- [OpenCode Download / Install](https://opencode.ai/download)
- [OpenCode Docs: Providers](https://opencode.ai/docs/providers)
- [OpenCode Docs: Config](https://opencode.ai/docs/config)
- [OpenCode Docs: Models](https://opencode.ai/docs/models)
- [NVIDIA Kimi K2.5 Model Page](https://build.nvidia.com/moonshotai/kimi-k2.5)
- [NVIDIA Kimi K2.5 Model Card](https://build.nvidia.com/moonshotai/kimi-k2.5/modelcard)
- [NVIDIA API Reference](https://docs.api.nvidia.com/nim/reference/moonshotai-kimi-k2-5)
