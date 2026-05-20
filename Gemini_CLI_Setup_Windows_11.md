# Gemini CLI Setup (Windows 11)

Gemini CLI is a terminal-based AI assistant from Google. It runs locally via Node.js and connects to Gemini models with your Google account or API key.

This guide uses PowerShell on Windows 11 (no WSL required).

> **⚠️ Important: Gemini CLI is being replaced by Antigravity CLI on June 18, 2026.**
>
> Google announced at I/O 2026 that Gemini CLI is folding into the new Antigravity agent platform. On **June 18, 2026**, Gemini CLI stops serving requests for users on Google AI Pro/Ultra and free personal Google accounts. Enterprise Code Assist customers are unaffected.
>
> **What to do:** Read the [Antigravity CLI section](#migrating-to-antigravity-cli) at the bottom of this guide before investing heavily in Gemini CLI. For short-term experimentation it's still fine; for anything you'll rely on past June 18, install Antigravity CLI instead.

---

## 1. Prerequisites

### A) Node.js 20.0.0+

The official docs recommend Node.js 20.0.0 or newer.

**Check if you already have it:**

```powershell
node --version
npm --version
```

If you do not have Node.js:

1. Go to https://nodejs.org
2. Download the **LTS** version (Node 20+)
3. Run the installer with default options
4. Close and reopen PowerShell, then verify with the commands above

### B) Windows version and shell

The official recommended system specs list **Windows 11 24H2+** and a **bash or zsh** shell. If you run into issues in PowerShell, try Git Bash.

---

## 2. Install Gemini CLI

### Option A: Install globally with npm (recommended)

```powershell
npm install -g @google/gemini-cli
```

### Option B: Run without installing (npx)

```powershell
npx @google/gemini-cli
```

**Verify it installed (global):**

```powershell
gemini --version
```

> **"gemini is not recognized" error:** Close and reopen PowerShell. If it still fails, npm's global bin folder is not in your PATH. Run `npm config get prefix` and add its `\bin` subfolder to your system PATH.

---

## 3. Authenticate

Gemini CLI supports multiple authentication methods. Most users should log in with Google.

### Option A: Login with Google (recommended)

1. Start the CLI:

```powershell
gemini
```

2. Select **Login with Google**. A browser window will open for sign-in.

Your credentials are cached locally for future sessions.

### Free tier limits (personal Google account)

- **60 requests/min**
- **1,000 requests/day**
- Default model: **Gemini Flash** (currently Gemini 3 Flash or 2.5 Flash, depending on rollout)

Note: Pro models are no longer included in the free tier as of April 1, 2026.

---

## 4. Run Gemini CLI

Once installed and authenticated:

```powershell
gemini
```

---

## 5. Quick Start Summary

```powershell
# 1. Install Node.js 20+ from https://nodejs.org (LTS)

# 2. Install Gemini CLI
npm install -g @google/gemini-cli

# 3. Start and authenticate
gemini
```

---

## Migrating to Antigravity CLI

Antigravity CLI is Google's replacement for Gemini CLI. It's available **now**, built in Go (faster cold-start than the Node-based Gemini CLI), and keeps the most important features: agent skills, hooks, subagents, and extensions (renamed "plugins"). It also runs multiple agents in parallel for background tasks.

### Install Antigravity on Windows

1. Go to [https://antigravity.google/download](https://antigravity.google/download)
2. Pick **Download for x64** (or ARM64 if you're on a Snapdragon machine)
3. Run the installer — it goes to `C:\Program Files\Google\Antigravity` by default
4. Launch from Start menu or run `antigravity` in PowerShell

Sign in with the same Google account you used for Gemini CLI; your free tier carries over.

### System requirements

Windows 11, 8GB RAM minimum, 2GB free disk.

### What changes vs Gemini CLI

- New binary name (`antigravity` instead of `gemini`)
- More agent-centric workflow (build/plan/parallel agents)
- Gemini CLI extensions now load as Antigravity plugins
- Free tier supports "several full agent runs per day" with a week of artifact history

For most casual use, the differences are small. If you have a `.gemini/` config in your project, Antigravity reads a similar `.antigravity/` config — see the [migration docs](https://antigravity.google/docs/migration).

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `gemini` not recognized | Restart PowerShell. If still broken, add npm global `\bin` to PATH (`npm config get prefix`) |
| Auth prompt repeats | Restart terminal and re-run `gemini` |
| 429 Too Many Requests | Hit free-tier rate limit (60 RPM / 1,000 RPD) — wait and retry, or use a different provider |
| "This model is not available on the free tier" | Pro models moved to paid as of April 1, 2026 — stick to Flash variants |
| Service unavailable after June 18, 2026 | Free tier Gemini CLI has sunset — install Antigravity CLI instead |

---

## References

- [Gemini CLI GitHub Repository](https://github.com/google-gemini/gemini-cli)
- [Gemini CLI Installation Guide](https://github.com/google-gemini/gemini-cli/blob/main/docs/get-started/installation.md)
- [Gemini CLI Authentication Guide](https://github.com/google-gemini/gemini-cli/blob/main/docs/get-started/authentication.md)
- [Gemini CLI Quotas & Pricing](https://geminicli.com/docs/resources/quota-and-pricing/)
- [Transitioning Gemini CLI to Antigravity CLI (official announcement)](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/)
- [Antigravity Download](https://antigravity.google/download)
