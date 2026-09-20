# Aider Setup with NVIDIA NIM Free API (Windows 11)

Aider is a terminal-based AI pair-programming tool that edits code in your local repo, makes git commits, and works with any OpenAI-compatible API — including NVIDIA's free NIM (NVIDIA Inference Microservices) endpoints, which host 80+ frontier models for free.

**Aider runs natively on Windows 11.** No WSL (Windows Subsystem for Linux) needed. Everything below uses PowerShell, which comes built into Windows 11.

> **Recommended model (September 2026):** `z-ai/glm-5.3`. Free on NVIDIA NIM, MIT-licensed, and the best combination of reliability and speed in the catalog as of 2026-09-20: it answered every probe in roughly 4 seconds.
>
> **⚠️ If you followed the August version of this guide, change your model.** That version recommended `deepseek-ai/deepseek-v4-flash-0731`, which has not been retired but has become severely slow. Probing it on 2026-09-20 produced timeouts at 120 seconds (twice), one success at **172 seconds**, and another timeout at **300 seconds**, for a 16-token reply. Because it eventually returns a valid `200` rather than an error, Aider just sits there, and it reads as a broken config rather than a slow model. Switch to `z-ai/glm-5.3`.
>
> **Also retired since the August version:** `z-ai/glm-5.2` (EOL 2026-08-21, superseded by `glm-5.3`), `stepfun-ai/step-3.7-flash` (2026-08-28), `openai/gpt-oss-120b` (2026-09-03) and `minimaxai/minimax-m3` (2026-09-09). All four now return `410 Gone`.
>
> **`moonshotai/kimi-k3` is working again.** The August guide flagged it as unstable after a sustained backing-function outage. That is over — it answered every probe on 2026-09-20. It is slow, at 85 to 105 seconds per call, so it is a deliberate choice rather than a default, but it is no longer broken.

> **Aider version note (September 2026):** the current release is still **0.86.2** (2026-02-12), and the last commit to `main` is still 2026-05-22 — unchanged since the August review, now roughly seven months without a release and four without a commit. It still works well and remains the best git-native option, but treat it as maintenance-mode software rather than something that will gain features soon.

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
3. Pick any model, for example [GLM-5.3](https://build.nvidia.com/z-ai/glm-5.3)
4. Click **"Get API Key"** (or "Build with this NIM")
5. Copy the key — it starts with `nvapi-`

A single `nvapi-` key works for every model on NVIDIA's free tier. You do not need a different key per model.

> **Choose your key's expiry deliberately.** NVIDIA now asks for a time-to-live when you generate a key, anywhere from one hour to "never expires." Pick something long, and write down the date. An expired key is genuinely confusing to diagnose, see the table below.

> **If a model 404s or hangs forever:** some models need a one-time per-account registration before your key can reach them. Open that model's page on build.nvidia.com and click **"Try API"** once, then retry. This is not limited to fresh keys. On 2026-09-20, on a key that had been working for a month, `moonshotai/kimi-k2.6` and `nvidia/nemotron-nano-3-30b-a3b` both returned `Function '<uuid>': Not found for account`. Both are listed in the catalog, so `/v1/models` is not a reliable guide to what your key can actually call.
>
> **But check the clock before you blame the gate.** A hang can also just be a slow model. If a model you have already used successfully starts hanging, raise your timeout before you go hunting for a registration problem — see the `deepseek-v4-flash-0731` warning at the top of this guide.

### Decoding NVIDIA's error responses

| Response | Meaning | Fix |
|---|---|---|
| `410 Gone` with an EOL date | Model is retired. Returned even if your key is bad, since the EOL check runs before auth. | Use a current model ID |
| `404 page not found` | Slug does not exist, often a wrong org prefix | Verify the slug on build.nvidia.com |
| `404` + `Function '<uuid>': Not found for account` | Model exists, your account is not registered for it | Click "Try API" on its model page |
| `403 Authorization failed` on **every** live model | Key is invalid or expired | Generate a new key |
| `404` with an empty body + `Nvcf-Status: errored` | The backing function is down. Not an account problem | Use another model; retry later |
| `503 Service temporarily overloaded` | Brief capacity blip | Retry immediately, it usually clears |
| `503 ResourceExhausted: Worker local ... (78/32)` | That model's **shared** worker pool is oversubscribed across all users | Retry later or pick a less popular model |
| Hangs with no response | Registration gap failing silently, **or** simply a slow model | New key: click "Try API". Otherwise raise your timeout |

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
aider --model openai/z-ai/glm-5.3
```

The `openai/` prefix tells Aider to use the OpenAI-compatible API base. The rest is the NVIDIA model ID.

If the folder isn't a Git repo yet, initialize one first:

```powershell
cd C:\path\to\your\project
git init
aider --model openai/z-ai/glm-5.3
```

Aider may show warnings about an unfamiliar model — this is normal. To suppress them:

```powershell
aider --model openai/z-ai/glm-5.3 --no-show-model-warnings
```

---

## 6. Picking a Model

NVIDIA NIM hosts 80+ models. A live catalog call on 2026-09-20 returned 82, down from 103 a month earlier. Browse the full list at [https://build.nvidia.com/models](https://build.nvidia.com/models). Each of these was sent a real completion request on 2026-09-20, and the response time is what came back:

| Model ID | Context | Observed latency | Best for |
|---|---|---|---|
| `z-ai/glm-5.3` | 1M | ~4s | **Default recommendation.** MIT-licensed agentic coder, reasoning model |
| `meta/muse-glimmer-30b` | — | ~2s | Fastest thing tested here. Good for quick mechanical edits |
| `nvidia/nemotron-3-super-120b-a12b` | 1M | ~1s | NVIDIA's own MoE, good general coder, low latency |
| `nvidia/nemotron-3-ultra-550b-a55b` | 262K | ~3s | NVIDIA's largest, 550B / 55B active |
| `nvidia/nemotron-3.5-lightning-30b-a3b` | — | ~1s | Built for always-on agents, very low latency |
| `openai/gpt-oss-20b` | 131K | ~1s | Small, fast, OpenAI open weights |
| `z-ai/glm-5.3-flash` | 1M | ~55s | Smaller GLM. Reliable but unexpectedly slow on NIM |
| `moonshotai/kimi-k3` | 1M | 85–105s | 2.8T params, multimodal, strongest on paper. Working again, but slow |
| `google/gemma-4-31b-it` | — | ~115s | Works, but slow, and may need the "Try API" click first |
| `deepseek-ai/deepseek-v4-flash-0731` | 1M | **172s or timeout** | Last month's default. **Avoid** — see the warning at the top |

Latency on NIM is shared capacity, so your numbers will differ. The point of the column is the *order of magnitude*: the difference between `glm-5.3` and `deepseek-v4-flash-0731` is not noise.

To use any of these with Aider, swap the model ID:

```powershell
aider --model openai/nvidia/nemotron-3-super-120b-a12b
```

### Retired, do not use

These model IDs appeared in earlier versions of this guide and now fail. Each confirmed by a live `410 Gone` response on 2026-09-20, with the EOL date read out of the response body:

| Dead model ID | EOL date | Use instead |
|---|---|---|
| `z-ai/glm-5.2` | 2026-08-21 | `z-ai/glm-5.3` |
| `meta/llama-3.3-70b-instruct` | 2026-08-26 | `nvidia/nemotron-3-super-120b-a12b` |
| `stepfun-ai/step-3.7-flash` | 2026-08-28 | `moonshotai/kimi-k3` (also multimodal) |
| `openai/gpt-oss-120b` | 2026-09-03 | `openai/gpt-oss-20b` |
| `minimaxai/minimax-m3` | 2026-09-09 | `z-ai/glm-5.3` |
| `deepseek-ai/deepseek-v4-flash` | 2026-08-07 | `z-ai/glm-5.3` |
| `deepseek-ai/deepseek-v4-pro` | 2026-08-07 | `z-ai/glm-5.3` |
| `meta/llama-4-maverick-17b-128e-instruct` | 2026-07-27 | `nvidia/nemotron-3-super-120b-a12b` |
| `qwen/qwen3-coder-480b-a35b-instruct` | 2026-06-11 | `z-ai/glm-5.3` |
| `moonshotai/kimi-k2-instruct` | 2026-05-12 | `moonshotai/kimi-k3` |
| `zai-org/glm-5.3` | never existed | `z-ai/glm-5.3` (note the hyphen) |

> Model IDs change often, and they change without much warning. The authoritative list is the URL slug on the model card page at build.nvidia.com. Before committing a model ID into a config, send it one throwaway request: a `410` names the retirement date, a `404` means the slug is wrong.

---

## 7. Project Config File (So You Don't Have to Type Flags Every Time)

Create a file called `.aider.conf.yml` in your project folder with this content:

```yaml
# .aider.conf.yml

model: openai/z-ai/glm-5.3
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
GLM-5.3, GLM-5.3 Flash, Kimi K3, and Nemotron 3 Super all support **1,000,000 tokens** of context, which is enough for most full repos. Nemotron 3 Ultra sits at 262K and GPT-OSS at 131K. Across the whole catalog the range runs from 4K to 1M, so check the model card before assuming you can feed it a large repo.

### Edit format
If a model has trouble producing correct code edits (often happens with smaller / older models), try the `whole` format:

```powershell
aider --model openai/z-ai/glm-5.3 --edit-format whole
```

This makes the model output entire files instead of diffs. Uses more tokens but is more reliable.

### Architect mode (two-model workflow)
Aider can use one model to plan and another to apply edits. This often produces better results:

```powershell
aider --model openai/z-ai/glm-5.3 --editor-model openai/nvidia/nemotron-3-super-120b-a12b
```

GLM-5.3 plans, Nemotron 3 Super applies the edits. You get the stronger model's reasoning without paying its latency on every mechanical file write.

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
    model      = "z-ai/glm-5.3"
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
aider --model openai/z-ai/glm-5.3 --no-show-model-warnings
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
aider --model openai/z-ai/glm-5.3
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
| API returns 404 Not Found | Model slug is wrong. Check the URL slug on build.nvidia.com. A common one: it is `z-ai/glm-5.3`, not `zai-org/glm-5.3` |
| API returns 404 with `Function '<uuid>': Not found for account` | Model exists but your account is not registered for it. Click "Try API" on its model page |
| Request hangs forever with no response | Same registration gap, failing silently. Click "Try API" on its model page |
| API returns 403 on **every** model you try | Your key is invalid or expired, not a model problem. Generate a new one |
| API returns 410 Gone | Model is retired. The error names the EOL date. See the retired-models table in Section 6 |
| API returns 429 Too Many Requests | Hit free tier rate limit (~40 RPM, shared across models) — wait 60 seconds and retry |
| `pip install aider-chat` fails on Python 3.13+ | Aider 0.86.2 requires Python <3.13 (its metadata pins `>=3.10,<3.13`). Use the uv or `aider-install` method, which brings its own 3.12 |
| Aider shows model warnings | Add `--no-show-model-warnings` or set `show-model-warnings: false` in config |
| Edits are garbled or wrong | Try `--edit-format whole` for more reliable (but token-heavy) edits |

---

## References

- [Aider: AI Pair Programming](https://aider.chat/)
- [Aider Installation](https://aider.chat/docs/install.html)
- [Aider OpenAI-Compatible API Docs](https://aider.chat/docs/llms/openai-compat.html)
- [Aider Configuration](https://aider.chat/docs/config/aider_conf.html)
- [Aider In-Chat Commands](https://aider.chat/docs/usage/commands.html)
- [Aider Release History](https://aider.chat/HISTORY.html)
- [NVIDIA NIM Model Catalog](https://build.nvidia.com/models)
- [Kimi K3 on NVIDIA NIM](https://build.nvidia.com/moonshotai/kimi-k3)
- [GLM-5.3 on NVIDIA NIM](https://build.nvidia.com/z-ai/glm-5.3)
- [NVIDIA NIM API Reference](https://docs.api.nvidia.com/nim/reference)
- [Python Downloads](https://www.python.org/downloads/)
- [Git for Windows](https://git-scm.com/download/win)
