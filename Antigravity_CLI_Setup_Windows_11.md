# Antigravity CLI Setup (Windows 11)

Antigravity CLI is Google's terminal AI coding agent, and the official replacement for Gemini CLI. It ships as a Go binary called `agy` and connects to Gemini models with your Google account.

This guide uses PowerShell on Windows 11 (no WSL required).

> **⚠️ Gemini CLI shut down for free and consumer users on June 18, 2026.**
>
> On that date Gemini CLI (and the Gemini Code Assist IDE extensions) stopped serving requests for free personal Google accounts and for Google AI Pro and Ultra subscribers. If you are looking for the old Gemini CLI guide, it is gone because the product is gone. See [What happened to Gemini CLI](#what-happened-to-gemini-cli) below.
>
> **Still working:** organizations on Gemini Code Assist Standard or Enterprise licenses, and anyone calling Gemini through a paid API key, kept uninterrupted access.

---

## 1. Prerequisites

Antigravity CLI is a self-contained Go binary. Unlike Gemini CLI, **it does not need Node.js**.

- Windows 11 (24H2 or newer recommended)
- 8GB RAM minimum, 2GB free disk
- A Google account

---

## 2. Install Antigravity CLI

### Option A: PowerShell installer (recommended)

```powershell
irm https://antigravity.google/cli/install.ps1 | iex
```

### Option B: Desktop app installer

The Antigravity desktop app bundles the CLI.

1. Go to [https://antigravity.google/download](https://antigravity.google/download)
2. Pick **Download for x64** (or ARM64 on a Snapdragon machine)
3. Run the installer — it defaults to `C:\Program Files\Google\Antigravity`

**Verify it installed:**

```powershell
agy --version
```

Current version as of 2026-10-24 is **1.4.7**, read from the installer's own release manifest, up from 1.2.7 a month earlier. Antigravity CLI is still shipping releases most weekdays and updates itself in the background. On Windows the binary installs under `%LOCALAPPDATA%`, in the `agy\bin` subfolder.

> **What changed since 1.2.7 that you will notice:**
>
> - **Good for Windows:** the command sandbox now runs **without administrator rights**, with Python, Git and npm working inside it (1.2.17). Plugin install and removal got more reliable on Windows (1.3.1, 1.3.2), and if PowerShell 7 from the Microsoft Store fails, it now falls back to Windows PowerShell (1.3.3).
> - **New:** `/compact` to shrink context on demand (1.3.5), `--effort` and `/effort` for reasoning effort (1.2.11), a `/plugin` marketplace manager (1.3.2), a built-in `image-generator` subagent (1.2.16), and `@name` to message a subagent directly (1.2.9). `-p "/usage"` now prints quota reset times in your local time (1.2.15), which helps with the quota problem below.
> - **Not on Windows yet:** **Auto mode** (`--mode auto`, or Shift+Tab), added in 1.3.5 for Linux and macOS, with Windows support "coming later."
> - **Breaking changes, if you script it:** default verbosity dropped from high to medium (1.3.0); `--dangerously-skip-permissions` no longer auto-approves plans in `/plan` mode (1.3.2); headless runs that end on a model or agent error exit with code 3 (1.2.10); an unknown `--agent` name exits with code 1 (1.3.4); `--json-schema` rejects non-object roots (1.2.14). In `/diff`, `j`/`k` now move the cursor instead of switching files (1.3.0).
> - **Quality of life on rate limits:** it now honours server-requested retry delays, stops early on daily or billing caps instead of retrying forever (1.2.13), and cancels and retries model connections that stall for 5 minutes (1.3.6).

> **"agy is not recognized" error:** Close and reopen PowerShell so the PATH change takes effect. The binary is `agy`, not `antigravity` and not `gemini`.

---

## 3. Authenticate

Start the CLI and sign in:

```powershell
agy
```

Sign in with your Google account when prompted. Credentials are cached in the system keyring for future sessions.

Once you are in a session, `/config` shows your current setup.

---

## 4. Free Tier Limits (Read This Before You Rely On It)

This is the biggest practical difference from Gemini CLI, and the most common complaint about the migration.

| | Gemini CLI (retired) | Antigravity CLI |
|---|---|---|
| Quota unit | Requests | Compute-based agent runs |
| Free allowance | 1,000 requests/day | ~20 agent requests/day (community estimate; Google publishes no number) |
| Refresh | Daily | Weekly ceiling. Older reports describe a ~5-hour rolling cycle under it; October reports mostly describe weekly only |
| Visible to the agent | Yes | **No** — check `/usage` yourself |

> **Google still does not publish the free-tier numbers.** Every figure in this section comes from community reports, and as of October 2026 they disagree on whether there is still a 5-hour refresh at all. What they agree on: Tab completions and command requests are unmetered, only agent work counts, and a heavy session can lock the weekly bucket in under an hour. Run `agy -p "/usage"` to see your own reset time.

Practical notes:

- **Subagents consume quota in parallel.** A single `/goal` run that fans out to subagents can burn a large chunk of your allowance in one go.
- **The agent cannot see its own remaining quota.** Run `/usage` regularly; there is no warning before you hit the wall.
- Hitting the weekly ceiling can mean a multi-day cooldown, not a five-hour one.
- Reports of the 5-hour refresh not landing on schedule are common.

### If you need more quota

| Plan | Cost | Roughly |
|---|---|---|
| Free | $0 | ~20 agent requests/day |
| Google AI Pro | $20/mo | Baseline paid quota |
| Google AI Ultra | $100/mo | ~5x Pro |
| Google AI Ultra Max | $200/mo | ~20x Pro |
| Credits | ~$0.01 each | Bulk, roughly $199 for 20,000 |

Treat the free tier as enough to evaluate the multi-agent workflow, not as a daily driver. If you want a free terminal coding agent you can lean on all day, use [Aider](Aider_Setup_NVIDIA_NIM.md) or [OpenCode](OpenCode_Setup_NVIDIA_NIM.md) against NVIDIA NIM instead.

> **The 20/day figure is not new, and not a Gemini CLI migration penalty.** Antigravity launched in November 2025 with 250 requests/day and was cut to ~20/day by December 2025, six months before Gemini CLI shut down. What changed in June 2026 is that Gemini CLI users lost their 1,000/day and landed on a quota that had already been reduced.

---

## 5. Essential Commands

Type these inside an `agy` session:

| Command | What it does |
|---|---|
| `/goal` | Run autonomously to completion without stopping to ask |
| `/grill-me` | Agent asks clarifying questions before it starts |
| `/plan` | Plan first, then review the plan before anything runs |
| `/browser` | Let the agent use a browser (JS-heavy sites, scraping) |
| `/schedule` | Set a one-time or recurring automated run |
| `/compact` | Shrink the conversation context on demand (1.3.5+) |
| `/effort` | Set reasoning effort (1.2.11+) |
| `/diff` | Review changes, with a colored commit tree (1.3.6+) |
| `/plugin` | Browse and manage plugins (1.3.2+) |
| `/usage` | Show quota and rate-limit status |
| `/model` | Switch models mid-session |
| `/config` | Show current configuration |

---

## 6. Quick Start Summary

```powershell
# 1. Install (no Node.js needed)
irm https://antigravity.google/cli/install.ps1 | iex

# 2. Launch and sign in with Google
agy

# 3. Check what quota you actually have
#    (type this inside the session)
/usage
```

---

## What Happened to Gemini CLI

Google announced at I/O 2026 that Gemini CLI was folding into the Antigravity agent platform, and pulled the plug for consumer tiers on **June 18, 2026**.

The change was contentious. Gemini CLI was Apache 2.0 and had taken more than 6,000 merged pull requests from outside contributors over roughly a year. Antigravity CLI is a **closed-source Go binary**, so those contributors ended up having donated work to a product whose successor they cannot inspect or use for free at the same level.

> **The Gemini CLI repo is not dead, though.** It still ships nightly builds (`v0.67.0-nightly` on 2026-10-24), because Gemini Code Assist Standard and Enterprise customers and paid API-key users kept access. What ended in June was the free Google-account login. This review found no reliable confirmation that a free AI Studio key works with it, so do not count on that.

### Migrating a Gemini CLI setup

- **Extensions → plugins.** Antigravity keeps Agent Skills, Hooks, Subagents, and Extensions, with Extensions renamed to plugins. There is an `agy plugin import` command to bring the old ones across.
  - Known limitation: some MCP servers show up in the UI but do not actually work with the agent yet.
- **Config directory.** A project's `.gemini/` config has an `.antigravity/` equivalent.
- **Per-model quotas and granular control over modifications** were features of Gemini CLI that Antigravity does not currently replicate.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `gemini` not recognized / stopped working | Expected. Gemini CLI retired June 18, 2026 for free and Pro/Ultra users. Install Antigravity CLI |
| `agy` not recognized | Restart PowerShell. Confirm the installer completed and added it to PATH |
| Quota exhausted after only a couple of prompts | Subagents consume quota in parallel. Run `/usage`, and prefer `/grill-me` over `/goal` for smaller tasks |
| Quota did not refresh after 5 hours | Known and widely reported. You may be against the weekly ceiling, which has a longer cooldown |
| Every model reports "Individual quota reached" | Reported in September and October 2026, with baseline quota not restoring until a fixed date days out. That is the weekly ceiling, not a transient error, and waiting is the only fix. `agy -p "/usage"` shows the reset time |
| Shift+Tab does not enter Auto mode | Auto mode is Linux and macOS only as of 1.4.7. Windows support is "coming later" |
| A script that used `--dangerously-skip-permissions` now stops at plans | Intentional since 1.3.2: it no longer auto-approves `/plan` mode |
| Headless run exits with code 3 | Since 1.2.10, a run that ends on a model or agent error exits 3. Check the output for the model error |
| Imported MCP server appears but does not work | Known limitation of `agy plugin import` |
| Auth prompt repeats | Restart the terminal and re-run `agy` |

---

## References

- [Antigravity Download](https://antigravity.google/download)
- [Transitioning Gemini CLI to Antigravity CLI (official announcement)](https://github.com/google-gemini/gemini-cli/discussions/27274)
- [Google blog: an important update on Gemini CLI](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/)
- [Antigravity usage and rate limits](https://antigravity.im/limits)
- [Gemini CLI GitHub Repository (archived reference)](https://github.com/google-gemini/gemini-cli)
