# PC Setup Guide

Everything you need on a fresh Windows machine before running any project in this stack. Complete this once, then follow the individual project SETUP.md.

---

## 1. PowerShell execution policy

By default Windows blocks scripts. Open PowerShell as Administrator and run:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

---

## 2. Python

Download Python 3.11 or newer from [python.org](https://python.org/downloads).

During install:
- Check **"Add Python to PATH"**
- Check **"Install for all users"** (optional but recommended)

Verify:
```powershell
python --version
pip --version
```

---

## 3. Git

Download from [git-scm.com](https://git-scm.com/download/win). Use default options.

Configure your identity:
```powershell
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

---

## 4. GitHub CLI

Download from [cli.github.com](https://cli.github.com). After install:

```powershell
gh auth login
```

Follow the prompts — choose GitHub.com, HTTPS, and authenticate via browser. This lets you clone private repos and push without a password.

---

## 5. VS Code

Download from [code.visualstudio.com](https://code.visualstudio.com).

Recommended extensions (install from the Extensions sidebar):
- **Python** (Microsoft)
- **Pylance** (Microsoft)
- **GitLens**
- **Thunder Client** (lightweight API tester, useful for Flask projects)

---

## 6. Claude Code

Claude Code is an AI coding assistant that runs in the terminal and understands your entire project.

Install:
```powershell
npm install -g @anthropic-ai/claude-code
```

> Requires Node.js — download from [nodejs.org](https://nodejs.org) if you don't have it.

Authenticate:
```powershell
claude
```

Follow the prompts to log in with your Anthropic account. After setup, run `claude` from inside any project folder to start a session.

---

## 7. Project-specific tools

Install only what the projects you're using require:

### ngrok — for sharing local apps publicly
*(used by: golisano-study-tools)*
```powershell
winget install Ngrok.Ngrok
```
Then authenticate at [dashboard.ngrok.com](https://dashboard.ngrok.com/get-started/your-authtoken):
```powershell
ngrok config add-authtoken YOUR_TOKEN
```

---

### Google Cloud SDK — for GCP services
*(used by: Inventory)*

Download from [cloud.google.com/sdk](https://cloud.google.com/sdk/docs/install).

After install:
```powershell
gcloud init
gcloud auth application-default login
```

---

### Tesseract OCR — for document scanning
*(used by: PaperVault)*

Download the Windows installer from:
https://github.com/UB-Mannheim/tesseract/wiki

Install to the default path (`C:\Program Files\Tesseract-OCR\`). The app auto-detects it there.

---

### Ollama — for running local AI models
*(used by: local-ai-lab, optionally by Inventory and PaperVault)*

Download from [ollama.com](https://ollama.com). Ollama installs as a Windows background service.

Pull a model after install:
```powershell
ollama pull gemma4       # recommended if you have 16 GB VRAM
ollama pull llama3.2     # good for 8 GB VRAM
```

Verify it's running:
```powershell
curl http://localhost:11434/api/version
```

---

## 8. Verify everything

Run through this checklist before starting a project:

```powershell
python --version          # 3.11+
git --version
gh auth status
node --version            # for Claude Code
claude --version
```

Then follow the `SETUP.md` for whichever project you're setting up.
