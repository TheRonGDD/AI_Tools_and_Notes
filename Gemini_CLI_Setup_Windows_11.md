# Gemini CLI Setup (Windows 11)

Gemini CLI is a terminal-based AI assistant from Google. It runs locally via Node.js and connects to Gemini models with your Google account or API key.

This guide uses PowerShell on Windows 11 (no WSL required).

---

## 1. Prerequisites

### A) Node.js 20.0.0+ (recommended)

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

### B) Windows version and shell (recommended)

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

## Troubleshooting

| Problem | Solution |
|---|---|
| `gemini` not recognized | Restart PowerShell. If still broken, add npm global `\bin` to PATH (`npm config get prefix`). |
| Auth prompt repeats | Restart terminal and re-run `gemini`.  |

---

## References

- [Gemini CLI GitHub Repository](https://github.com/google-gemini/gemini-cli)
- [Gemini CLI Installation Guide](https://github.com/google-gemini/gemini-cli/blob/main/docs/get-started/installation.md)
- [Gemini CLI Authentication Guide](https://github.com/google-gemini/gemini-cli/blob/main/docs/get-started/authentication.md)
