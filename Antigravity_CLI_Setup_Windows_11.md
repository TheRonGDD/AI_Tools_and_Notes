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
| Free allowance | 1,000 requests/day | ~20 agent requests/day |
| Refresh | Daily | ~5-hour rolling cycle, under a weekly ceiling |
| Visible to the agent | Yes | **No** — check `/usage` yourself |

Practical notes:

- **Subagents consume quota in parallel.** A single `/goal` run that fans out to subagents can burn a large chunk of your allowance in one go.
- **The agent cannot see its own remaining quota.** Run `/usage` regularly; there is no warning before you hit the wall.
- Hitting the weekly ceiling can mean a multi-day cooldown, not a five-hour one.
- Reports of the 5-hour refresh not landing on schedule are common.

Treat the free tier as enough to evaluate the multi-agent workflow, not as a daily driver. If you want a free terminal coding agent you can lean on all day, use [Aider](Aider_Setup_NVIDIA_NIM.md) or [OpenCode](OpenCode_Setup_NVIDIA_NIM.md) against NVIDIA NIM instead.

---

## 5. Essential Commands

Type these inside an `agy` session:

| Command | What it does |
|---|---|
| `/goal` | Run autonomously to completion without stopping to ask |
| `/grill-me` | Agent asks clarifying questions before it starts |
| `/browser` | Let the agent use a browser (JS-heavy sites, scraping) |
| `/schedule` | Set a one-time or recurring automated run |
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
| Imported MCP server appears but does not work | Known limitation of `agy plugin import` |
| Auth prompt repeats | Restart the terminal and re-run `agy` |

---

## References

- [Antigravity Download](https://antigravity.google/download)
- [Transitioning Gemini CLI to Antigravity CLI (official announcement)](https://github.com/google-gemini/gemini-cli/discussions/27274)
- [Google blog: an important update on Gemini CLI](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/)
- [Antigravity usage and rate limits](https://antigravity.im/limits)
- [Gemini CLI GitHub Repository (archived reference)](https://github.com/google-gemini/gemini-cli)
