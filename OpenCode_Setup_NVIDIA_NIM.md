# OpenCode Setup with NVIDIA NIM Free API (Windows 11)

OpenCode is an open-source (MIT) AI coding agent from Anomaly. It supports any OpenAI-compatible API — including NVIDIA's free NIM (NVIDIA Inference Microservices) endpoints, which host 100+ frontier models for free.

**OpenCode runs natively on Windows 11** via Scoop, Chocolatey, or the universal installer. WSL is not required.

> **Recommended model (August 2026):** `deepseek-ai/deepseek-v4-flash-0731`. Free on NVIDIA NIM, 1M-token context, and the most reliable model in the catalog in testing: it answered every request across a full day of probing.
>
> **Caveat on `moonshotai/kimi-k3` (observed 2026-08-20):** it is listed in `/v1/models` and answered normally in the morning, but by late afternoon it returned `404` with an empty body and an `Nvcf-Status: errored` header on every attempt across several hours. That header means the request reached NVIDIA's function router and the backing function failed, which is different from the per-account `Function ... Not found for account` gate. Treat K3 as **unstable on NIM right now**: worth trying, not worth making your default. Re-tested at 13:44 and 13:58 the same day, after the guides were first corrected: still `404` with `Nvcf-Status: errored` every time, so this is a sustained outage of the backing function rather than a brief blip. `deepseek-ai/deepseek-v4-flash-0731` answered on every single attempt and is the safer primary.
>
> **If you followed the July version of this guide, your config is broken.** `deepseek-ai/deepseek-v4-flash` and `deepseek-ai/deepseek-v4-pro` both reached end of life on **2026-08-07** and now return `410 Gone`. The `zai-org/glm-5.2` entry was also wrong: the correct slug is `z-ai/glm-5.2`. Section 4 below has a corrected config you can paste over the old one.

> **What's new in OpenCode:** now at **v1.18.19** (2026-08-20) and past **199K stars**. It is no longer terminal-only, shipping as a **TUI, a desktop app, and an IDE extension**, with tabbed multi-session management in the desktop build. There is also **OpenCode Zen**, a curated managed-access set of models the team benchmarked for coding agents, which is separate from the free NVIDIA NIM setup described here.

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
2. Sign in or create a free NVIDIA Developer account (email plus **phone verification** — still no credit card)
3. Pick any model, for example [DeepSeek V4 Flash 0731](https://build.nvidia.com/deepseek-ai/deepseek-v4-flash-0731)
4. Click **"Get API Key"** (or "Build with this NIM")
5. Copy the key — it starts with `nvapi-`

A single `nvapi-` key works for all 100+ models on NVIDIA's free tier.

> **Choose your key's expiry deliberately.** NVIDIA now asks for a time-to-live when you generate a key, from one hour up to "never expires." Pick something long and note the date. An expired key returns `403 Authorization failed` on every live model, which reads like a catalog problem rather than an auth problem.

> **If a model 404s or hangs forever:** some models need a one-time per-account registration before your key can reach them. Open that model's page on build.nvidia.com and click **"Try API"** once, then retry. Expect this on a fresh key: it is common, not an edge case. On a brand-new key tested 2026-08-20, `openai/gpt-oss-120b`, `google/gemma-4-31b-it`, and `meta/llama-3.3-70b-instruct` each hung for a full four minutes with no response, and `moonshotai/kimi-k2.6` returned `Function '<uuid>': Not found for account`. All four are listed in the catalog, so `/v1/models` is not a reliable guide to what your key can actually call.

### Decoding NVIDIA's error responses

| Response | Meaning | Fix |
|---|---|---|
| `410 Gone` with an EOL date | Model is retired. Returned even if your key is bad, since the EOL check runs before auth. | Use a current model ID |
| `404 page not found` | Slug does not exist, often a wrong org prefix | Verify the slug on build.nvidia.com |
| `404` + `Function '<uuid>': Not found for account` | Model exists, your account is not registered for it | Click "Try API" on its model page |
| `403 Authorization failed` on **every** live model | Key is invalid or expired | Generate a new key |
| Hangs with no response | Same registration gap, failing silently | Click "Try API" on its model page |

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

> **Not just a terminal app anymore.** OpenCode also ships a desktop app and an IDE extension. The NVIDIA NIM provider config in Section 4 is shared across all three, so setting it up once covers whichever surface you use.

> **"opencode is not recognized" error:** Close and reopen your terminal. If using npm and it still fails, run `npm config get prefix` and add its `\bin` folder to your system PATH.

---

## 4. Configure NVIDIA NIM as a Provider

OpenCode looks for its global config in `C:\Users\YOUR_USERNAME\.config\opencode\`, and it accepts more than one filename there:

```
C:\Users\YOUR_USERNAME\.config\opencode\config.json
C:\Users\YOUR_USERNAME\.config\opencode\opencode.json
```

Use `.jsonc` on either name if you want comments in the file.

> **If both files exist, OpenCode reads both and merges them.** Verified on OpenCode 1.18.19 (2026-08-20): a provider defined only in `opencode.json` showed up alongside everything in `config.json`, with no warning that two files were in play. That is an easy way to spend an hour wondering why an edit had no effect, or why a provider you thought you deleted is still listed. Pick one filename and confirm the other does not exist:
>
> ```powershell
> dir $env:USERPROFILE\.config\opencode\*.json*
> ```

Create or edit that file with the following content, replacing the API key with yours. Every model ID here was verified callable on 2026-08-20:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "nim": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "NVIDIA NIM",
      "options": {
        "baseURL": "https://integrate.api.nvidia.com/v1",
        "apiKey": "nvapi-YOUR_KEY_HERE"
      },
      "models": {
        "deepseek-ai/deepseek-v4-flash-0731": {
          "name": "DeepSeek V4 Flash 0731",
          "limit": {
            "context": 1000000,
            "output": 32768
          }
        },
        "z-ai/glm-5.2": {
          "name": "GLM-5.2",
          "limit": {
            "context": 1048576,
            "output": 131072
          }
        },
        "minimaxai/minimax-m3": {
          "name": "MiniMax M3",
          "limit": {
            "context": 1048576,
            "output": 65536
          }
        },
        "nvidia/nemotron-3-super-120b-a12b": {
          "name": "Nemotron 3 Super 120B",
          "limit": {
            "context": 1000000,
            "output": 32768
          }
        },
        "stepfun-ai/step-3.7-flash": {
          "name": "Step 3.7 Flash",
          "limit": {
            "context": 262144,
            "output": 65536
          }
        }
      }
    }
  }
}
```

> **Note the `z-ai/` prefix on GLM-5.2.** Earlier versions of this guide used `zai-org/glm-5.2`, which returns a bare `404 page not found`. If GLM is the only model that fails for you, this is why.

> **Why the provider key above is `nim` and not `nvidia`.** OpenCode ships a built-in model catalog (from models.dev). If your provider key matches a provider that catalog already knows, your config is **merged into** it rather than replacing it. Naming the key `nvidia` is the intuitive choice and it is the wrong one: with a config listing 4 curated models, `/models` offered **99** NVIDIA entries, among them `deepseek-ai/deepseek-v4-flash`, `deepseek-ai/deepseek-v4-pro`, `meta/llama-4-maverick-17b-128e-instruct` and `qwen/qwen3-coder-480b-a35b-instruct` — all four retired, all four returning `410 Gone` the moment you pick one. Your `models` map does not restrict the picker, it only adds to it.
>
> Any key the built-in catalog does not recognise, such as `nim`, suppresses the merge, and the picker then shows exactly the models you listed. Verified both ways on 2026-08-20: key `nvidia` gave 99 models, key `nim` gave 4. The only cost is that model references become `nim/<model-id>`.

### Two places hold API keys, and they are not the same place

The `apiKey` in the config above is one way to authenticate. The other is `opencode auth login`, which stores credentials **outside** your config entirely:

```
C:\Users\YOUR_USERNAME\.local\share\opencode\auth.json
```

List what is stored there with:

```powershell
opencode auth list
```

> **The trap: authenticating an NVIDIA key against the wrong provider.** `opencode auth login` shows a menu of provider names. Because NVIDIA hosts Moonshot's Kimi models, picking **Moonshot AI** and pasting your `nvapi-` key looks like the way to reach Kimi. It is not. That credential is used against Moonshot's own endpoint at `api.moonshot.ai`, which does not accept NVIDIA keys — a direct test with an `nvapi-` key returns `401`. The result is a stored credential that can never work, sitting in a file you are not looking at while you debug your config.
>
> Every NVIDIA-hosted model, Kimi included, goes through the NIM provider block in your config and the `integrate.api.nvidia.com` base URL. Nothing NVIDIA-related belongs under a Moonshot, DeepSeek, or Z.ai credential.
>
> If you hit this, remove the bad entry with `opencode auth logout` and pick the provider you mis-keyed, or delete its object from `auth.json` by hand. Back the file up first.

### Setting a default model

Add a `model` field at the top level so OpenCode picks it without prompting:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "nim/deepseek-ai/deepseek-v4-flash-0731",
  "provider": {
    ...
  }
}
```

The format is `<provider-key>/<model-id>` — here, `nim/` is the provider key from your config, and the rest is the NVIDIA model ID. If you named your provider something else, match it here.

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
3. Find **NVIDIA NIM → DeepSeek V4 Flash 0731** (or whichever model) and select it

Or type `/models` directly in the chat input.

---

## 7. Picking a Model

NVIDIA NIM hosts 100+ models. A live catalog call on 2026-08-20 returned 103. Browse the full list at [https://build.nvidia.com/models](https://build.nvidia.com/models). These were verified callable on that date:

| Model ID | Context | Best for |
|---|---|---|
| `deepseek-ai/deepseek-v4-flash-0731` | 1M | **Default recommendation.** Fast, strong coding, 284B MoE / 13B active |
| `moonshotai/kimi-k3` | 1M | 2.8T params, multimodal, strongest on paper. **Unstable on NIM, see the caveat at the top.** Left out of the config above deliberately |
| `z-ai/glm-5.2` | 1M | Excellent agentic coder, MIT-licensed, 744B MoE / 40B active |
| `minimaxai/minimax-m3` | 1M | 428B MoE / 23B active, multimodal, 59% on SWE-Bench Pro |
| `nvidia/nemotron-3-super-120b-a12b` | 1M | NVIDIA's own MoE, good general coder, low latency |
| `nvidia/nemotron-3-ultra-550b-a55b` | 262K | NVIDIA's largest, 550B / 55B active |
| `nvidia/nemotron-3.5-lightning-30b-a3b` | — | Fastest responses in the catalog, good for quick edits |
| `stepfun-ai/step-3.7-flash` | 256K | 198B MoE with a vision encoder |
| `openai/gpt-oss-20b` | 131K | Small, fast, OpenAI open weights |

To add more models, add entries to the `models` map in `config.json` and restart OpenCode.

### Retired, do not use

These model IDs appeared in earlier versions of this guide and now fail. Confirmed by live `410 Gone` responses on 2026-08-20:

| Dead model ID | EOL date | Use instead |
|---|---|---|
| `deepseek-ai/deepseek-v4-flash` | 2026-08-07 | `deepseek-ai/deepseek-v4-flash-0731` |
| `deepseek-ai/deepseek-v4-pro` | 2026-08-07 | `z-ai/glm-5.2` |
| `meta/llama-4-maverick-17b-128e-instruct` | 2026-07-27 | `nvidia/nemotron-3-super-120b-a12b` |
| `qwen/qwen3-coder-480b-a35b-instruct` | 2026-06-11 | `z-ai/glm-5.2` |
| `moonshotai/kimi-k2-instruct` | 2026-05-12 | `deepseek-ai/deepseek-v4-flash-0731` |
| `zai-org/glm-5.2` | never existed | `z-ai/glm-5.2` (note the hyphen) |

> Model IDs change often, and without much warning. The authoritative list is the URL slug on the model card page at build.nvidia.com. Before committing a model ID into a config, send it one throwaway request: a `410` names the retirement date, a `404` means the slug is wrong.

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
NVIDIA's free tier is roughly **40 requests per minute**, and that budget is **shared across all models** rather than allocated per model. If you get a 429, wait a minute and retry. Agentic tools like OpenCode burn requests faster than chat does, so this is a real ceiling on long autonomous runs.

### Context window
DeepSeek V4 Flash 0731, GLM-5.2, MiniMax M3, Kimi K3, and Nemotron 3 Super all support **1,000,000 tokens** of context, which covers most full repos. OpenCode automatically includes relevant files. Note that the `limit.context` values in your config are what OpenCode uses to decide when to compact a session, so setting them accurately matters.

### Keep your API key out of git
Your API key is stored in plain text in `config.json`. The default location (`~/.config/opencode/`) is outside any project, but double-check that you haven't accidentally checked in a copy.

### MCP support
OpenCode supports MCP (Model Context Protocol) servers — same protocol used by Claude Desktop. Configure them under a `mcp` key in `config.json`. See the OpenCode docs for examples.

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
    model      = "deepseek-ai/deepseek-v4-flash-0731"
    messages   = @(@{ role = "user"; content = "Say hello in one sentence." })
    max_tokens = 50
} | ConvertTo-Json -Depth 3

Invoke-RestMethod -Uri "https://integrate.api.nvidia.com/v1/chat/completions" -Method Post -Headers $headers -Body $body
```

If you get a response with a `choices` array containing a message, your key is working.

### Test OpenCode end-to-end

The fastest check is non-interactive, so you see the raw result without a TUI in the way:

```powershell
cd C:\path\to\your\project
opencode run "Reply with exactly: OPENCODE OK"
```

The header line names the model that actually answered, which is the quickest way to confirm your default `model` field took effect. To test one specific model without editing the config:

```powershell
opencode run --model nim/z-ai/glm-5.2 "Reply with exactly: OPENCODE OK"
```

Then try the TUI:

```powershell
opencode
```

Type `What files are in this directory?` and press Enter. If OpenCode responds with a list of files, everything is working.

> **If a model answers cleanly by `curl` but rambles or produces garbled text in OpenCode,** run it two or three more times before changing anything. OpenCode sends a large system prompt plus tool definitions, and these free endpoints occasionally degenerate under that load in a way a one-line `curl` test never shows. One such run was observed on `deepseek-ai/deepseek-v4-flash-0731` on 2026-08-20; three immediately following runs with the identical prompt were clean. Treat a single bad generation as transient rather than as a broken config.

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

# 2. Create config file at C:\Users\YOUR_USERNAME\.config\opencode\config.json
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
| `/models` doesn't show NVIDIA NIM | Check that `config.json` is valid JSON (no trailing commas, correct path). Also check you do not have a second `opencode.json` in the same folder — both are read and merged |
| API returns 401 Unauthorized | Double-check the `nvapi-` key in `config.json`. Also run `opencode auth list`: a stored credential for another provider can shadow what you expect (see Section 4) |
| API returns 404 Not Found | Model slug is wrong. Check the URL slug on build.nvidia.com. A common one: it is `z-ai/glm-5.2`, not `zai-org/glm-5.2` |
| API returns 404 with `Function '<uuid>': Not found for account` | Model exists but your account is not registered for it. Click "Try API" on its model page |
| Request hangs forever with no response | Same registration gap, failing silently. Click "Try API" on its model page |
| API returns 403 on **every** model you try | Your key is invalid or expired, not a model problem. Generate a new one |
| API returns 410 Gone | Model is retired. The error names the EOL date. See the retired-models table in Section 7 |
| `/models` lists far more models than your config does | Your provider key collides with OpenCode's built-in models.dev catalog and got merged into it. Rename the key to something it does not know, e.g. `nim`. See Section 4 |
| A model you picked from the list 410s immediately | Same cause: the built-in catalog still offers retired NVIDIA IDs. Pick from your own `models` map, not the merged list |
| API returns 429 Too Many Requests | Hit free tier rate limit (~40 RPM, shared across models) — wait 60 seconds and retry |
| Slow responses | Normal for large reasoning models. Try `nvidia/nemotron-3.5-lightning-30b-a3b` if you want speed above all |
| Config changes not picked up | Restart OpenCode after editing `config.json`. If it still looks stale, check for a second `opencode.json` in `~/.config/opencode/` |

---

## References

- [OpenCode Official Site](https://opencode.ai)
- [OpenCode GitHub](https://github.com/anomalyco/opencode)
- [OpenCode Download / Install](https://opencode.ai/docs/install/)
- [OpenCode Docs: Providers](https://opencode.ai/docs/providers/)
- [OpenCode Docs: Config](https://opencode.ai/docs/config/)
- [OpenCode Docs: Models](https://opencode.ai/docs/models/)
- [NVIDIA NIM Model Catalog](https://build.nvidia.com/models)
- [Kimi K3 on NVIDIA NIM](https://build.nvidia.com/moonshotai/kimi-k3)
- [DeepSeek V4 Flash 0731 on NVIDIA NIM](https://build.nvidia.com/deepseek-ai/deepseek-v4-flash-0731)
- [NVIDIA NIM API Reference](https://docs.api.nvidia.com/nim/reference)
