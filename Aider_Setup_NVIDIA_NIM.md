# Aider Setup with NVIDIA NIM Free API (Windows 11)

Aider is a terminal-based AI pair-programming tool that edits code in your local repo, makes git commits, and works with any OpenAI-compatible API — including NVIDIA's free NIM (NVIDIA Inference Microservices) endpoints, which host 100+ frontier models for free.

**Aider runs natively on Windows 11.** No WSL (Windows Subsystem for Linux) needed. Everything below uses PowerShell, which comes built into Windows 11.

> **Recommended model (July 2026):** `deepseek-ai/deepseek-v4-flash` — DeepSeek's 284B MoE coding model with a 1M-token context window. Free on NVIDIA NIM, optimized for coding and agentic tasks. The Kimi K2.5 model previously featured in this guide was retired in April 2026; the configuration steps below work the same for any NVIDIA-hosted model — just swap the model ID.

> **Aider version note (July 2026):** the current release is **0.86.2** (February 2026). Aider has not shipped a release in several months, so treat 0.86.2 as the stable target rather than waiting on a newer one.

---

## 1. Prerequisites (Install These First)

### A) Python 3.10 - 3.12

Aider requires **Python >=3.10, <3.13**. Python 3.13 and 3.14 are **not** supported by the current release (0.86.2) despite what some older write-ups claim — the package metadata caps it below 3.13.

If your system Python is 3.13+, use the `uv tool` or `aider-install` methods below, which install a compatible Python (3.12) for Aider in an isolated environment rather than fighting your system install.

**Check if you already have Python:**

```powershell
python --version
```

If you don't have Python at all:

1. Go to [https://www.python.org/downloads/](https://www.python.org/downloads/)
2. Download **Python 3.12.x** (the most compatible release)
3. Run the installer
4. **IMPORTANT:** Check the box that says **"Add python.exe to PATH"** at the bottom of the first screen
5. Click "Install Now"
6. Close and reopen PowerShell, then verify:

```powershell
python --version
pip --version
```

> **Microsoft Store Python:** If `python` opens the Microsoft Store instead of running, go to **Settings > Apps > Advanced app execution aliases** and turn OFF the "App Installer" entries for `python.exe` and `python3.exe`.

### B) Git

Aider uses Git to track changes and make commits. Most coding workflows require it.

**Check if you already have it:**

```powershell
git --version
```

If you don't have Git:

1. Go to [https://git-scm.com/download/win](https://git-scm.com/download/win)
2. Download and run the installer
3. Use all the default options (just click Next through the installer)
4. Close and reopen PowerShell, then verify:

```powershell
git --version
```

**First-time Git setup** (if you've never used Git on this machine):

```powershell
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### C) Visual C++ Build Tools (Usually Not Needed)

Some Python packages need a C compiler. If the Aider install fails with an error about "Microsoft Visual C++ 14.0 or greater is required":

1. Go to [https://visualstudio.microsoft.com/visual-cpp-build-tools/](https://visualstudio.microsoft.com/visual-cpp-build-tools/)
2. Download and run "Build Tools for Visual Studio"
3. Select **"Desktop development with C++"** and install
4. Retry the Aider install

Most users will NOT need this step. Only do it if you see the error.

---

## 2. Get Your Free NVIDIA API Key

1. Go to [https://build.nvidia.com](https://build.nvidia.com)
2. Sign in or create a free NVIDIA Developer account (email plus **phone verification** — still no credit card)
3. Pick any model — for example [DeepSeek V4 Flash](https://build.nvidia.com/deepseek-ai/deepseek-v4-flash)
4. Click **"Get API Key"** (or "Build with this NIM")
5. Copy the key — it starts with `nvapi-`

A single `nvapi-` key works for all 100+ models on NVIDIA's free tier. You do not need a different key per model.

> **If a model 403s or hangs forever:** some model families need a one-time per-family registration before your key can reach them. Open that model's page on build.nvidia.com and click **"Try API"** once, then retry. This trips people up most often on newer models like `kimi-k2.6` and `deepseek-v4-pro`, where the symptom is an indefinite hang rather than a clean error.

---

## 3. Install Aider

Open PowerShell and run **one** of these:

### Option A: uv (recommended — fastest, handles Python for you)

If you don't have uv:

```powershell
python -m pip install uv
```

Then install Aider in its own managed environment:

```powershell
uv tool install --force --python python3.12 --with pip aider-chat@latest
```

### Option B: One-liner installer

Works even if your system Python is 3.13+ — it installs its own Python 3.12.

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://aider.chat/install.ps1 | iex"
```

### Option C: aider-install helper

```powershell
python -m pip install aider-install
aider-install
```

### Option D: Plain pip (Python 3.10-3.12 only)

```powershell
python -m pip install aider-chat
```

**Verify it installed:**

```powershell
aider --version
```

> **"aider is not recognized" error:** Close and reopen PowerShell. If it still doesn't work, Python's Scripts folder isn't in your PATH. Find it with `python -c "import site; print(site.getusersitepackages())"` and add the `Scripts` subfolder to your system PATH, or use `python -m aider` instead of `aider`.

---

## 4. Configure Your API Key

### Option A: Set permanently (recommended)

```powershell
setx OPENAI_API_BASE "https://integrate.api.nvidia.com/v1"
setx OPENAI_API_KEY "nvapi-YOUR_KEY_HERE"
```

**You MUST close and reopen PowerShell** after running `setx` for the changes to take effect.

Verify they stuck:

```powershell
echo $env:OPENAI_API_BASE
echo $env:OPENAI_API_KEY
```

### Option B: Set per-session (temporary, gone when you close the window)

```powershell
$env:OPENAI_API_BASE = "https://integrate.api.nvidia.com/v1"
$env:OPENAI_API_KEY = "nvapi-YOUR_KEY_HERE"
```

---

## 5. Launch Aider

Navigate to any project folder and run:

```powershell
cd C:\path\to\your\project
aider --model openai/deepseek-ai/deepseek-v4-flash
```

The `openai/` prefix tells Aider to use the OpenAI-compatible API base. The rest is the NVIDIA model ID.

If the folder isn't a Git repo yet, initialize one first:

```powershell
cd C:\path\to\your\project
git init
aider --model openai/deepseek-ai/deepseek-v4-flash
```

Aider may show warnings about an unfamiliar model — this is normal. To suppress them:

```powershell
aider --model openai/deepseek-ai/deepseek-v4-flash --no-show-model-warnings
```

---

## 6. Picking a Model

NVIDIA NIM hosts 120+ models, roughly 99 of them free-tier callable. Browse the full catalog at [https://build.nvidia.com/models](https://build.nvidia.com/models). Some good picks for coding (July 2026):

| Model ID | Best for |
|---|---|
| `deepseek-ai/deepseek-v4-flash` | Default recommendation — fast, 1M context, strong coding |
| `deepseek-ai/deepseek-v4-pro` | Higher quality on hard problems, slower (1.6T MoE) |
| `zai-org/glm-5.2` | Current strongest open-weight agentic coder |
| `qwen/qwen3-coder-480b-a35b-instruct` | Code-specialist Qwen3 |
| `nvidia/nemotron-3-super-120b-a12b` | NVIDIA's own flagship MoE, good general coder |
| `moonshotai/kimi-k2.6` | Long-horizon agentic coding (256K context) — but slow latency |
| `meta/llama-4-maverick-17b-128e-instruct` | Meta's latest |

To use any of these with Aider, just swap the model ID:

```powershell
aider --model openai/qwen/qwen3-coder-480b-a35b-instruct
```

> Model IDs change over time. The authoritative list is the URL slug on the model card page on build.nvidia.com. New models land quickly — GLM-5.2 showed up on NIM about two weeks after its public release.

---

## 7. Project Config File (So You Don't Have to Type Flags Every Time)

Create a file called `.aider.conf.yml` in your project folder with this content:

```yaml
# .aider.conf.yml

model: openai/deepseek-ai/deepseek-v4-flash
show-model-warnings: false
auto-commits: true
show-diffs: true
```

With this file in place, just run `aider` with no flags from that directory.

**Global config (applies to all projects):** Place the same file at `C:\Users\YOUR_USERNAME\.aider.conf.yml`.

---

## 8. Essential Aider Commands

Once inside an aider session, these are the commands you'll use most:

| Command | What it does |
|---|---|
| `/add file.py` | Add a file to the chat for editing |
| `/drop file.py` | Remove a file from context |
| `/read-only file.py` | Add file as read-only reference |
| `/ls` | List all files and their status |
| `/ask <question>` | Ask about code without making edits |
| `/diff` | Show changes since last message |
| `/run <command>` | Execute a shell command |
| `/test <command>` | Run tests, feed failures back to the AI |
| `/git <command>` | Run a git command |
| `/tokens` | Show token usage |
| `/clear` | Clear chat history |
| `/model <name>` | Switch model mid-session |
| `/architect` | Enable architect mode (two-model workflow) |
| `/exit` | Quit aider |

### Basic workflow example

```
> /add src/main.py
> Add error handling to the parse_config function
> /diff
> /test python -m pytest tests/
> /exit
```

You type plain English to describe what you want changed. Aider edits the files directly and auto-commits to Git.

---

## 9. Tips

### Free tier rate limits
NVIDIA's free tier is roughly **40 requests per minute**, and that budget is **shared across all models** rather than allocated per model. If you get a 429, wait a minute and retry.

### Context windows
DeepSeek V4 Flash and Pro both support **1,000,000 tokens** of context — enough for most full repos. Kimi K2.6 gives you 256K. Free Llama models on NVIDIA are typically capped at 128K. Across the whole catalog the range is 8K to 1M, so check the model card before assuming you can feed it a large repo.

### Edit format
If a model has trouble producing correct code edits (often happens with smaller / older models), try the `whole` format:

```powershell
aider --model openai/deepseek-ai/deepseek-v4-flash --edit-format whole
```

This makes the model output entire files instead of diffs. Uses more tokens but is more reliable.

### Architect mode (two-model workflow)
Aider can use one model to plan and another to apply edits. This often produces better results:

```powershell
aider --model openai/deepseek-ai/deepseek-v4-pro --editor-model openai/deepseek-ai/deepseek-v4-flash
```

---

## 10. Verify Everything Works

### Test your API key (before even installing Aider)

Paste this into PowerShell to confirm your NVIDIA key works:

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

If you get a response with a message, your key is working.

### Test Aider end-to-end

```powershell
mkdir C:\temp\aider-test
cd C:\temp\aider-test
git init
echo "# Test" > README.md
git add . && git commit -m "init"
aider --model openai/deepseek-ai/deepseek-v4-flash --no-show-model-warnings
```

Then type: `Create a hello.py that prints "Hello World"` and press Enter.

---

## Quick Start Summary

```powershell
# 1. Install Python 3.10-3.12 from https://www.python.org/downloads/
#    (check "Add python.exe to PATH" during install)
#    3.13+ is NOT supported by aider 0.86.2 — uv handles this for you

# 2. Install Git from https://git-scm.com/download/win

# 3. Install Aider via uv (recommended)
python -m pip install uv
uv tool install --force --python python3.12 --with pip aider-chat@latest

# 4. Set your NVIDIA API key (restart terminal after this)
setx OPENAI_API_BASE "https://integrate.api.nvidia.com/v1"
setx OPENAI_API_KEY "nvapi-YOUR_KEY_HERE"

# 5. Use it (after restarting terminal)
cd C:\your\project
aider --model openai/deepseek-ai/deepseek-v4-flash
```

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `python` opens Microsoft Store | Settings > Apps > Advanced app execution aliases > turn off python.exe entries |
| `aider is not recognized` | Close/reopen PowerShell. If still broken, use `python -m aider` instead |
| `pip` not found | Reinstall Python and check "Add python.exe to PATH" |
| Install fails with "Visual C++ 14.0 required" | Install [Build Tools for Visual Studio](https://visualstudio.microsoft.com/visual-cpp-build-tools/) with "Desktop development with C++" |
| `setx` variables not working | You must close and reopen PowerShell after running setx |
| API returns 401 Unauthorized | Double-check your `nvapi-` key is correct |
| API returns 404 Not Found | Model ID may be wrong or model retired — check build.nvidia.com for current ID |
| API returns 403, or request hangs forever | Model family needs one-time registration — open its page on build.nvidia.com and click "Try API", then retry |
| API returns 410 Gone | Model has been deprecated — pick a different one |
| API returns 429 Too Many Requests | Hit free tier rate limit (~40 RPM, shared across models) — wait 60 seconds and retry |
| `pip install aider-chat` fails on Python 3.13+ | Aider 0.86.2 requires Python <3.13. Use the uv or `aider-install` method, which brings its own 3.12 |
| Aider shows model warnings | Add `--no-show-model-warnings` or set `show-model-warnings: false` in config |
| Edits are garbled or wrong | Try `--edit-format whole` for more reliable (but token-heavy) edits |

---

## References

- [Aider: AI Pair Programming](https://aider.chat/)
- [Aider Installation](https://aider.chat/docs/install.html)
- [Aider OpenAI-Compatible API Docs](https://aider.chat/docs/llms/openai-compat.html)
- [Aider Configuration](https://aider.chat/docs/config/aider_conf.html)
- [Aider In-Chat Commands](https://aider.chat/docs/usage/commands.html)
- [NVIDIA NIM Model Catalog](https://build.nvidia.com/models)
- [DeepSeek V4 Flash on NVIDIA NIM](https://build.nvidia.com/deepseek-ai/deepseek-v4-flash)
- [NVIDIA NIM API Reference](https://docs.api.nvidia.com/nim/reference)
- [Python Downloads](https://www.python.org/downloads/)
- [Git for Windows](https://git-scm.com/download/win)
