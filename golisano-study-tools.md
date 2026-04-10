# Setup Guide — Golisano Study Tools

Flask + Supabase study dashboard for Golisano Institute for Business & Entrepreneurship courses. Supports user accounts, quiz history, and class-wide analytics. Shareable via ngrok.

---

## Prerequisites

- Python 3.11+
- A [Supabase](https://supabase.com) account (free tier works)
- (Optional) [ngrok](https://ngrok.com) account for sharing with classmates

---

## 1. Clone and create virtual environment

```powershell
git clone <your-repo-url>
cd golisano-study-tools
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

---

## 2. Set up Supabase

1. Create a new project at [supabase.com](https://supabase.com)
2. Go to **SQL Editor** and run the full contents of `supabase/schema.sql` — this creates the three tables (`profiles`, `quiz_attempts`, `quiz_responses`) and all RLS policies
3. Go to **Authentication → Providers → Email** and disable "Confirm email" (optional but recommended for dev/classroom use)
4. Grab your project credentials from **Settings → API**:
   - Project URL
   - `anon` public key

---

## 3. Create `.env`

Create a `.env` file in the project root:

```
SUPABASE_URL=https://your-project-ref.supabase.co
SUPABASE_ANON_KEY=your-anon-key
FLASK_SECRET_KEY=any-random-string-you-generate
```

Generate a secret key with:
```powershell
python -c "import secrets; print(secrets.token_hex(32))"
```

---

## 4. Run the app

```powershell
.\.venv\Scripts\python.exe app.py
```

App runs at `http://127.0.0.1:5000`.

---

## 5. Share with others (optional)

Install ngrok and authenticate:
```powershell
winget install Ngrok.Ngrok
ngrok config add-authtoken YOUR_TOKEN_FROM_NGROK_DASHBOARD
```

In a second terminal (while the app is running):
```powershell
ngrok http 5000
```

Share the `/help` URL with classmates — not the bare URL. The `/help` page explains what the tool is and has Register/Log In buttons.

> **Note:** The ngrok URL changes every session on the free tier. Reshare each time.

---

## Adding a new course

See the **"Recreating This for Another Class"** section in `CLAUDE.md` for the full step-by-step — it covers folder structure, routes in `app.py`, nav links, and all four template types.

---

## Stack summary

| Layer | Technology |
|-------|-----------|
| Backend | Flask (Python) |
| Database + Auth | Supabase (PostgreSQL + RLS) |
| Frontend | Jinja2 templates + vanilla JS |
| Sharing | ngrok |
| Hosting (local) | Flask dev server |
