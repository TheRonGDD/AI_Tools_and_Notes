# OpenCode Setup with NVIDIA NIM Free API (Windows 11)

OpenCode is an open-source (MIT) AI coding agent from Anomaly. It supports any OpenAI-compatible API — including NVIDIA's free NIM (NVIDIA Inference Microservices) endpoints, which host 80+ frontier models for free.

**OpenCode runs natively on Windows 11** via Scoop, Chocolatey, or the universal installer. WSL is not required.

> **Recommended model (October 2026):** `z-ai/glm-5.3`, unchanged from September. Free on NVIDIA NIM, MIT-licensed, and it answered every probe on 2026-10-24, though at 7 to 27 seconds per reply rather than September's ~4s. **New fast option:** `minimaxai/minimax-m3.1-flash`, which answered in 0.2 to 0.7 seconds and made a correct tool call in 2.3s. Both are in the config in Section 4.
>
> **⚠️ Retired since the September version:** `moonshotai/kimi-k3` (EOL **2026-10-15**) and `deepseek-ai/deepseek-v4-flash-0731` (EOL 2026-09-21), both now `410 Gone`. Kimi K3 was in the config block September's guide told you to paste, so if you used it, remove that entry. Its successor `deepseek-ai/deepseek-v4.1-flash` is new on NIM and has the **same latency problem**: 166 seconds for a two-token reply. Leave it out.
>
> **`nvidia/nemotron-3-super-120b-a12b` has been dropped from the config below.** It worked on 2026-09-20 and returned `Function '<uuid>': Not found for account` on 2026-10-24 on the same key. Clicking "Try API" on its model page should restore it, but a model that silently needs re-registration is not a good default.

> **⚠️ OpenCode 2 is out, and it does not read `config.json`.** OpenCode 2.0.0 shipped 2026-09-11 and is at **2.1.4** (2026-10-23). It reads `opencode.json` / `opencode.jsonc` only. `config.json`, which earlier versions of this guide used, was [dropped from V2's config discovery](https://github.com/anomalyco/opencode/pull/34645) on purpose. If you upgrade with a `config.json`, your providers silently vanish. This guide now uses **`opencode.json`**, which works on both 1.x and 2. See [Section 12](#12-opencode-2) before upgrading. Once the file is renamed, the setup in this guide works on 2.1.4 unchanged (tested 2026-10-24).

> **What's new in OpenCode 1.x:** now at **v1.19.4** (2026-10-22) and past **221K stars**, up from 208K. 1.x stays on its own release line: as of 1.19.2 it will never offer you a 2.x build as a routine update. Two changes in 1.19 are worth knowing. **Updates are no longer installed automatically** (1.19.0); run `/update` yourself. And **session sharing has been discontinued** (1.19.1): existing share links stop working and the share commands are gone. OpenCode ships as a **TUI, a desktop app, and an IDE extension**, and there is also **OpenCode Zen**, a curated managed-access model set that is separate from the free NVIDIA NIM setup described here.

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
3. Pick any model, for example [GLM-5.3](https://build.nvidia.com/z-ai/glm-5.3)
4. Click **"Get API Key"** (or "Build with this NIM")
5. Copy the key — it starts with `nvapi-`

A single `nvapi-` key works for every model on NVIDIA's free tier.

> **Choose your key's expiry deliberately.** NVIDIA now asks for a time-to-live when you generate a key, from one hour up to "never expires." Pick something long and note the date. An expired key returns `403 Authorization failed` on every live model, which reads like a catalog problem rather than an auth problem.

> **If a model 404s or hangs forever:** some models need a one-time per-account registration before your key can reach them. Open that model's page on build.nvidia.com and click **"Try API"** once, then retry. This is not limited to fresh keys, and it is not even limited to models you have never used. On 2026-10-24, `nvidia/nemotron-3-super-120b-a12b`, which had answered on the same key a month earlier, returned `Function '<uuid>': Not found for account`, as did `moonshotai/kimi-k2.6` and `nvidia/nemotron-nano-3-30b-a3b`. All three are listed in the catalog, so `/v1/models` is not a reliable guide to what your key can actually call.
>
> **A hang is not always the gate.** It can simply be a slow model. If something you have already used successfully starts hanging, raise your timeout before assuming a registration problem — see the `deepseek-v4.1-flash` warning at the top.

### Decoding NVIDIA's error responses

| Response | Meaning | Fix |
|---|---|---|
| `410 Gone` with an EOL date | Model is retired. Returned even if your key is bad, since the EOL check runs before auth. | Use a current model ID |
| `404 page not found` | Slug does not exist, often a wrong org prefix | Verify the slug on build.nvidia.com |
| `404` + `Function '<uuid>': Not found for account` | Model exists, your account is not registered for it (can happen to a model that used to work) | Click "Try API" on its model page |
| `403 Authorization failed` on **every** live model | Key is invalid or expired | Generate a new key |
| `404` with an empty body + `Nvcf-Status: errored` | The backing function is down. Not an account problem | Use another model; retry later |
| `500 Failed to generate completions` | Backend generation failure on that model | Retry; switch models if it repeats |
| `503 Service temporarily overloaded` | Brief capacity blip | Retry immediately, it usually clears |
| `503 ResourceExhausted` (`All workers are busy` or `... (108/32)`) | That model's **shared** worker pool is oversubscribed across all users | Retry later or pick a less popular model |
| Hangs with no response | Registration gap failing silently, **or** simply a slow model | New key: click "Try API". Otherwise raise your timeout |

---

## 3. Install OpenCode

These install **OpenCode 1.x**, which is what the rest of this guide covers. For OpenCode 2, see [Section 12](#12-opencode-2).

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

As of 1.19.2 this script refuses to install 2.x.

### Option E: Download the binary

Go to the [GitHub releases page](https://github.com/anomalyco/opencode/releases) and download the Windows binary. Place it somewhere in your PATH.

**Verify it installed:**

```powershell
opencode --version
```

> **Homebrew users, check which major you got.** Homebrew core now tracks OpenCode **2** (2.1.4 as of 2026-10-24), not 1.x. On Windows this does not apply, but it matters if you follow this guide on a Mac.

> **Not just a terminal app anymore.** OpenCode also ships a desktop app and an IDE extension. The NVIDIA NIM provider config in Section 4 is shared across all three, so setting it up once covers whichever surface you use.

> **"opencode is not recognized" error:** Close and reopen your terminal. If using npm and it still fails, run `npm config get prefix` and add its `\bin` folder to your system PATH.

---

## 4. Configure NVIDIA NIM as a Provider

OpenCode looks for its global config in `C:\Users\YOUR_USERNAME\.config\opencode\`. **Use `opencode.json`:**

```
C:\Users\YOUR_USERNAME\.config\opencode\opencode.json
```

Use `opencode.jsonc` if you want comments in the file.

> **Why not `config.json`?** OpenCode 1.x accepts both `config.json` and `opencode.json`, and earlier versions of this guide used `config.json`. OpenCode 2 accepts **only** `opencode.json(c)`, and ignores `config.json` without a warning. Starting with `opencode.json` means an eventual upgrade to 2 does not silently drop your providers. If you already have a `config.json`, rename it.

> **If both files exist, OpenCode 1.x reads both and merges them.** Verified on OpenCode 1.18.19 (2026-08-20): a provider defined only in `opencode.json` showed up alongside everything in `config.json`, with no warning that two files were in play. That is an easy way to spend an hour wondering why an edit had no effect, or why a provider you thought you deleted is still listed. Keep exactly one file:
>
> ```powershell
> dir $env:USERPROFILE\.config\opencode\*.json*
> ```

Create or edit that file with the following content, replacing the API key with yours. Every model ID here answered a live completion **and** a live tool call on 2026-10-24:

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
        "z-ai/glm-5.3": {
          "name": "GLM-5.3",
          "limit": {
            "context": 1048576,
            "output": 131072
          }
        },
        "minimaxai/minimax-m3.1-flash": {
          "name": "MiniMax M3.1 Flash",
          "limit": {
            "context": 1048576,
            "output": 65536
          }
        },
        "nvidia/nemotron-3-ultra-550b-a55b": {
          "name": "Nemotron 3 Ultra 550B",
          "limit": {
            "context": 262144,
            "output": 32768
          }
        },
        "meta/muse-glimmer-30b": {
          "name": "Muse Glimmer 30B",
          "limit": {
            "context": 262144,
            "output": 32768
          }
        },
        "nvidia/nemotron-3.5-lightning-30b-a3b": {
          "name": "Nemotron 3.5 Lightning",
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

> **Note the `z-ai/` prefix on GLM-5.3.** Earlier versions of this guide used `zai-org/glm-5.2`, which returns a bare `404 page not found`. The org prefix is `z-ai`, with a hyphen. If GLM is the only model that fails for you, this is why.

> **The output limit on MiniMax M3.1 Flash is a conservative guess.** MiniMax has not published one; third-party listings disagree. 65,536 is safely below any figure reported. The Lightning context is also set conservatively at 262,144; OpenRouter serves the same model at 1M. Underestimating only makes OpenCode compact a little early.

> **Why the provider key above is `nim` and not `nvidia`.** OpenCode ships a built-in model catalog (from models.dev). If your provider key matches a provider that catalog already knows, your config is **merged into** it rather than replacing it. Naming the key `nvidia` is the intuitive choice and it is the wrong one: with a config listing 4 curated models, `/models` offered **99** NVIDIA entries, among them `deepseek-ai/deepseek-v4-flash`, `deepseek-ai/deepseek-v4-pro`, `meta/llama-4-maverick-17b-128e-instruct` and `qwen/qwen3-coder-480b-a35b-instruct` — all four retired, all four returning `410 Gone` the moment you pick one. Your `models` map does not restrict the picker, it only adds to it.
>
> Any key the built-in catalog does not recognise, such as `nim`, suppresses the merge, and the picker then shows exactly the models you listed. Verified both ways on 2026-08-20: key `nvidia` gave 99 models, key `nim` gave 4. The only cost is that model references become `nim/<model-id>`. OpenCode 2's provider docs describe the same behaviour for catalog provider IDs (the built-in catalog's models "still apply"), and V2 ignores the old `whitelist`/`blacklist` fields, so the `nim` name is still the way to get a clean picker.

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
> Every NVIDIA-hosted model goes through the NIM provider block in your config and the `integrate.api.nvidia.com` base URL. Nothing NVIDIA-related belongs under a Moonshot, DeepSeek, MiniMax, or Z.ai credential.
>
> If you hit this, remove the bad entry with `opencode auth logout` and pick the provider you mis-keyed, or delete its object from `auth.json` by hand. Back the file up first.

### Setting a default model

Add a `model` field at the top level so OpenCode picks it without prompting:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "nim/z-ai/glm-5.3",
  "provider": {
    ...
  }
}
```

The format is `<provider-key>/<model-id>` — here, `nim/` is the provider key from your config, and the rest is the NVIDIA model ID. If you named your provider something else, match it here. If GLM-5.3's 7 to 27 second turns feel slow for your work, `nim/minimaxai/minimax-m3.1-flash` is the fast alternative.

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
3. Find **NVIDIA NIM → GLM-5.3** (or whichever model) and select it

Or type `/models` directly in the chat input.

---

## 7. Picking a Model

NVIDIA NIM hosts 80+ models. A live catalog call on 2026-10-24 returned 85, up from 82 a month earlier. Browse the full list at [https://build.nvidia.com/models](https://build.nvidia.com/models). Each of these was sent a real completion request on 2026-10-24, and the fast ones were also given a tool and checked for a correct tool call:

| Model ID | Context | Observed latency | Best for |
|---|---|---|---|
| `z-ai/glm-5.3` | 1M | 7–27s | **Default recommendation.** MIT-licensed agentic coder, answered every probe |
| `minimaxai/minimax-m3.1-flash` | 1M | 0.2–0.7s | **New, and the fast pick.** Multimodal input, correct tool calls |
| `nvidia/nemotron-3-ultra-550b-a55b` | 262K | 1–7s | NVIDIA's largest, 550B / 55B active |
| `nvidia/nemotron-3.5-lightning-30b-a3b` | — | 1–2s | Built for always-on agents, very low latency |
| `meta/muse-glimmer-30b` | 262K | 1–9s | Built for agent harnesses |
| `nvidia/nemotron-3.5-super-vl-120b-a12b` | 1M | 1–3s, but flaky | **New.** Multimodal. One `500` and one 300s timeout in four calls |
| `nvidia/nemotron-3.5-bolt-e4b` | 128K | 0.3s | **New.** Small vision model, good for quick mechanical work |
| `openai/gpt-oss-20b` | 131K | 1–2s | Small, fast, OpenAI open weights |
| `stepfun-ai/step-5-preview` | 1M | 134s or timeout | **New** 600B preview. Too slow on NIM for now |
| `z-ai/glm-5.3-flash` | 1M | ~136s | Smaller GLM, but much slower than its big sibling on NIM |
| `deepseek-ai/deepseek-v4.1-flash` | 1M | **166s or timeout** | **Avoid**, same trap as its retired predecessor |
| `nvidia/nemotron-3-super-120b-a12b` | 1M | — | Needs a "Try API" click as of 2026-10-24 |

Latency on NIM is shared capacity, so your numbers will differ. The column is there for the order of magnitude, not the exact figure.

To add more models, add entries to the `models` map in `opencode.json` and restart OpenCode.

### Retired, do not use

These model IDs appeared in earlier versions of this guide and now fail. Each confirmed by a live `410 Gone`, with the EOL date read out of the response body:

| Dead model ID | EOL date | Use instead |
|---|---|---|
| `moonshotai/kimi-k3` | **2026-10-15** | `z-ai/glm-5.3` or `minimaxai/minimax-m3.1-flash` (also multimodal) |
| `deepseek-ai/deepseek-v4-flash-0731` | 2026-09-21 | `minimaxai/minimax-m3.1-flash` |
| `minimaxai/minimax-m3` | 2026-09-09 | `minimaxai/minimax-m3.1-flash` |
| `openai/gpt-oss-120b` | 2026-09-03 | `openai/gpt-oss-20b` (or `openai/gpt-oss-120b` on Groq) |
| `stepfun-ai/step-3.7-flash` | 2026-08-28 | `nvidia/nemotron-3.5-super-vl-120b-a12b` (also multimodal) |
| `meta/llama-3.3-70b-instruct` | 2026-08-26 | `nvidia/nemotron-3-ultra-550b-a55b` |
| `z-ai/glm-5.2` | 2026-08-21 | `z-ai/glm-5.3` |
| `deepseek-ai/deepseek-v4-flash` | 2026-08-07 | `z-ai/glm-5.3` |
| `deepseek-ai/deepseek-v4-pro` | 2026-08-07 | `z-ai/glm-5.3` |
| `meta/llama-4-maverick-17b-128e-instruct` | 2026-07-27 | `nvidia/nemotron-3-ultra-550b-a55b` |
| `qwen/qwen3-coder-480b-a35b-instruct` | 2026-06-11 | `z-ai/glm-5.3` |
| `moonshotai/kimi-k2-instruct` | 2026-05-12 | `z-ai/glm-5.3` |
| `zai-org/glm-5.3` | never existed | `z-ai/glm-5.3` (note the hyphen) |

> Model IDs change often, and without much warning. The authoritative list is the URL slug on the model card page at build.nvidia.com. Before committing a model ID into a config, send it one throwaway request: a `410` names the retirement date, a `404` means the slug is wrong.

---

## 8. Essential OpenCode Commands

Type these in the chat input while inside an OpenCode session:

| Command | What it does |
|---|---|
| `/models` | Browse and switch AI models |
| `/connect` | Add a new provider interactively |
| `/agents` | Switch between build and plan agents |
| `/update` | Install an OpenCode update (no longer automatic as of 1.19.0) |
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
GLM-5.3, MiniMax M3.1 Flash and Nemotron 3.5 Super VL all support about **1,000,000 tokens** of context, which covers most full repos. OpenCode automatically includes relevant files. Note that the `limit.context` values in your config are what OpenCode uses to decide when to compact a session, so setting them accurately matters.

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
    model      = "z-ai/glm-5.3"
    messages   = @(@{ role = "user"; content = "Say hello in one sentence." })
    max_tokens = 512
} | ConvertTo-Json -Depth 3

Invoke-RestMethod -Uri "https://integrate.api.nvidia.com/v1/chat/completions" -Method Post -Headers $headers -Body $body -TimeoutSec 120
```

If you get a response with a `choices` array containing a message, your key is working. GLM-5.3 is a reasoning model, so `max_tokens` is set high enough for it to finish thinking before it writes the visible reply, and the timeout allows for its slower October response times.

### Test OpenCode end-to-end

The fastest check is non-interactive, so you see the raw result without a TUI in the way:

```powershell
cd C:\path\to\your\project
opencode run "Reply with exactly: OPENCODE OK"
```

The header line names the model that actually answered, which is the quickest way to confirm your default `model` field took effect. To test one specific model without editing the config:

```powershell
opencode run --model nim/minimaxai/minimax-m3.1-flash "Reply with exactly: OPENCODE OK"
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

## 12. OpenCode 2

OpenCode 2.0.0 shipped on 2026-09-11 and is at **2.1.4** (2026-10-23), published to npm as `@opencode/cli` rather than `opencode-ai`. It is a separate major line: 1.x keeps getting releases (1.19.4 on 2026-10-22) and will not upgrade itself to 2.

**Tested on 2026-10-24** by upgrading a real Windows 11 install from 1.19.0 (npm) to 2.1.4, using the Section 4 config saved as `opencode.json` in V1 shape with no conversion:

- The V1-shaped config loaded as-is. `opencode models` showed exactly the 5 models in the `nim` block, so the `nim` key still prevents the models.dev merge on V2.
- `opencode run` worked on `nim/minimaxai/minimax-m3.1-flash` (~1s) and `nim/z-ai/glm-5.3` (~10s), and a real tool call (reading a file and answering from it) worked.
- A local MCP server (`type: "local"`, launched via `npx`) connected without changes; check with `opencode mcp list`.
- Credentials stored by V1's `opencode auth login` in `auth.json` were picked up (a Google API key, tested against `google/gemini-3.6-flash`).
- `opencode run -m` now takes `provider/model#variant`; plain `provider/model` still works.

The rest of this section comes from OpenCode's own migration guide, its source, and its package manifests.

### What breaks

| Change | Effect on a setup built from this guide |
|---|---|
| **`config.json` is no longer read** | Your providers disappear with no warning. Rename to `opencode.json` first. ([PR #34645](https://github.com/anomalyco/opencode/pull/34645)) |
| **V1 plugins do not run** | Plugins need porting to the new plugin API. The config key also changes from `plugin` to `plugins` |
| **TUI settings move** | Layered `tui.json(c)` files become one global `~/.config/opencode/cli.json`, auto-migrated |
| **Server API changed** | Anything that calls the OpenCode server needs the new client package |
| **Only `AGENTS.md` is discovered** | A `CLAUDE.md` fallback that V1 honoured is not picked up. Move those instructions into `AGENTS.md` |

### What still works

V2 detects V1-shaped configuration and translates it in memory without rewriting your file, so the NIM provider block in Section 4 should not need converting. If you want the native V2 shape, the renames are: `provider` → `providers`, `npm` → `package`, `options.baseURL` / `options.apiKey` → `settings.baseURL` / `settings.apiKey`. Do not mix the two shapes inside one provider; V2 does not infer formats per entry.

### Upgrading on Windows

Back up first. Your config, `~\.local\share\opencode\auth.json`, and the session data in `~\.local\share\opencode\` are all worth a copy. Then rename `config.json` to `opencode.json` and check 1.x still loads it (`opencode models`) **before** you upgrade, so a problem afterwards is clearly a V2 problem.

```powershell
# npm install (this is the path tested above)
npm uninstall -g opencode-ai
npm install -g @opencode/cli
opencode --version     # opencode v2.1.4

# Or from inside OpenCode 1.19+ (the explicit opt-in)
opencode upgrade --major

# Or with Scoop, which ships 2.x as a separate package
scoop uninstall opencode
scoop bucket add versions
scoop install versions/opencode2
```

Scoop's own note on the `opencode2` package says *"The v2 configuration is incompatible with v1. Uninstall 'opencode' first."* The binary is still called `opencode`.

> **Should you upgrade?** If you do not rely on V1 plugins, it is safe: the NIM setup in this guide worked unchanged once the file was named `opencode.json`. If you do rely on plugins, wait until they are ported.

---

## Quick Start Summary

```powershell
# 1. Install OpenCode 1.x (pick one)
scoop install opencode                # if you use Scoop
choco install opencode                # if you use Chocolatey
npm install -g opencode-ai            # if you have Node.js
# OR download binary from https://github.com/anomalyco/opencode/releases

# 2. Create config file at C:\Users\YOUR_USERNAME\.config\opencode\opencode.json
#    (see Section 4 above for the full contents; NOT config.json)

# 3. Launch OpenCode in your project directory
cd C:\path\to\your\project
opencode
```

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `opencode is not recognized` | Close/reopen terminal. If using npm install, add npm global bin to PATH: run `npm config get prefix` and add its `\bin` folder to system PATH. If using binary, ensure it's in a PATH directory |
| `/models` doesn't show NVIDIA NIM | Check that the config is valid JSON (no trailing commas, correct path). On OpenCode **2**, check the file is named `opencode.json`, not `config.json`. On 1.x, check you do not have both files |
| All my providers vanished after upgrading | You are on OpenCode 2 with a `config.json`. Rename it to `opencode.json`. See Section 12 |
| Plugins stopped working after upgrading | OpenCode 2 does not run V1 plugins. Downgrade to 1.x or wait for a port |
| API returns 401 Unauthorized | Double-check the `nvapi-` key in your config. Also run `opencode auth list`: a stored credential for another provider can shadow what you expect (see Section 4) |
| API returns 404 Not Found | Model slug is wrong. Check the URL slug on build.nvidia.com. A common one: it is `z-ai/glm-5.3`, not `zai-org/glm-5.3` |
| API returns 404 with `Function '<uuid>': Not found for account` | Model exists but your account is not registered for it, even if it worked last month. Click "Try API" on its model page |
| Request hangs forever with no response | Either the same registration gap, failing silently, or a very slow model such as `deepseek-v4.1-flash`. Try a fast model first to tell which |
| API returns 403 on **every** model you try | Your key is invalid or expired, not a model problem. Generate a new one |
| API returns 410 Gone | Model is retired. The error names the EOL date. See the retired-models table in Section 7. Kimi K3 is the most likely culprit this month |
| `/models` lists far more models than your config does | Your provider key collides with OpenCode's built-in models.dev catalog and got merged into it. Rename the key to something it does not know, e.g. `nim`. See Section 4 |
| A model you picked from the list 410s immediately | Same cause: the built-in catalog still offers retired NVIDIA IDs. Pick from your own `models` map, not the merged list |
| API returns 429 Too Many Requests | Hit free tier rate limit (~40 RPM, shared across models) — wait 60 seconds and retry |
| Groq: `Request too large ... on tokens per minute (TPM): Limit 8000` | OpenCode's own prompt is bigger than Groq's free per-minute token cap, so every Groq model fails. Not fixable in config; use NVIDIA NIM, or add a card to Groq for higher limits |
| Groq: `max_completion_tokens must be less than or equal to 16384` | Your `limit.output` for that Groq model is too high. Set it to 16384 for `qwen/qwen3.8-27b` |
| Slow responses | GLM-5.3 takes 7 to 27 seconds per turn as of October. Switch to `nim/minimaxai/minimax-m3.1-flash` if you want speed above all |
| A shared session link stopped working | Session sharing was discontinued in OpenCode 1.19.1. There is no replacement |
| OpenCode stopped updating itself | Intentional as of 1.19.0. Run `/update` |
| Config changes not picked up | Restart OpenCode after editing the config. If it still looks stale, check for a second config file in `~/.config/opencode/` |

---

## References

- [OpenCode Official Site](https://opencode.ai)
- [OpenCode GitHub](https://github.com/anomalyco/opencode)
- [OpenCode Download / Install](https://opencode.ai/docs/install/)
- [OpenCode Docs: Providers](https://opencode.ai/docs/providers/)
- [OpenCode Docs: Config](https://opencode.ai/docs/config/)
- [OpenCode Docs: Models](https://opencode.ai/docs/models/)
- [OpenCode 2: Migrate from V1](https://opencode.ai/v2/docs/migrate-v1)
- [OpenCode 2: Providers](https://opencode.ai/v2/docs/providers)
- [NVIDIA NIM Model Catalog](https://build.nvidia.com/models)
- [GLM-5.3 on NVIDIA NIM](https://build.nvidia.com/z-ai/glm-5.3)
- [NVIDIA NIM API Reference](https://docs.api.nvidia.com/nim/reference)
