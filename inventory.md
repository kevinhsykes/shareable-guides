# Setup Guide — Inventory

Automated personal inventory system. Gmail purchase emails are pushed to a Flask service via Pub/Sub, parsed by Claude AI, and stored in Firestore. Supports Excel export, warranty tracking, and optional local AI.

---

## Prerequisites

- Python 3.12+
- [Google Cloud SDK](https://cloud.google.com/sdk/docs/install) (`gcloud` CLI)
- A Google Cloud project with billing enabled
- An [Anthropic API key](https://console.anthropic.com)
- A Gmail account to monitor

---

## 1. Google Cloud setup

Enable the required APIs:
```bash
gcloud services enable firestore.googleapis.com \
  pubsub.googleapis.com \
  run.googleapis.com \
  gmail.googleapis.com \
  secretmanager.googleapis.com
```

Create a Firestore database (Native mode, any region):
```bash
gcloud firestore databases create --location=us-east1
```

Create the Pub/Sub topic for Gmail push notifications:
```bash
gcloud pubsub topics create gmail-push-notifications
gcloud pubsub subscriptions create gmail-push-sub \
  --topic=gmail-push-notifications \
  --push-endpoint=https://YOUR-CLOUD-RUN-URL/pubsub
```

---

## 2. Gmail OAuth token

Generate the OAuth token that allows the app to read Gmail:
```powershell
cd cloud-run
python get_gmail_token.py
```

This opens a browser for Google sign-in and writes `gmail_token.json`. Keep this file secret.

Set up the Gmail push watch (tells Gmail to notify your Pub/Sub topic):
```powershell
python setup_gmail_watch.py
```

> **Note:** Gmail watch expires every 7 days. Re-run `setup_gmail_watch.py` weekly, or automate it with Cloud Scheduler.

---

## 3. Create `.env`

```powershell
cd cloud-run
copy .env.example .env
```

Fill in `.env`:
```
GOOGLE_CLOUD_PROJECT=your-gcp-project-id
PUBSUB_TOPIC=projects/your-gcp-project-id/topics/gmail-push-notifications
ANTHROPIC_API_KEY=sk-ant-...
GMAIL_TOKEN_JSON=<paste full contents of gmail_token.json here>
PARSE_MODEL=claude-sonnet-4-20250514
PORT=8080
```

---

## 4. Install dependencies and run locally

```powershell
cd cloud-run
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
.\start-server.ps1
```

Server runs at `http://localhost:8080`.

---

## 5. Deploy to Cloud Run

```powershell
.\cloud-run\deploy.ps1          # full deploy
.\cloud-run\deploy.ps1 -DryRun  # preview only
```

In production, credentials come from Secret Manager instead of `.env`. Store each env var as a Secret Manager secret and reference them in your Cloud Run service config.

---

## 6. Export inventory to Excel

```powershell
cd cloud-run
$env:GOOGLE_CLOUD_PROJECT = "your-gcp-project-id"
python export_inventory.py
```

Writes `inventory_full.xlsx` to the current directory.

---

## Optional: Local AI (Ollama)

The app has optional local AI tasks for email routing, category classification, and merchant normalization. These are **disabled by default**.

To enable:
1. Install [Ollama](https://ollama.com) and pull a model (e.g. `ollama pull gemma4`)
2. Install the `local_ai_core` package (see `local-ai-lab/SETUP.md`)
3. Edit `cloud-run/config/local_ai.yaml` — set `enabled: true` and configure your model
4. Benchmark accuracy before enabling in production

---

## Stack summary

| Layer | Technology |
|-------|-----------|
| Backend | Flask (Python 3.12) |
| Email trigger | Gmail API + Google Pub/Sub |
| AI parsing | Anthropic Claude API |
| Database | Google Firestore |
| Hosting | Google Cloud Run |
| Export | Excel (openpyxl) |
| Optional AI | Ollama / LM Studio via `local_ai_core` |
