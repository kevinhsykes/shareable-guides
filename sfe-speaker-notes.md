# Setup Guide — SFE Speaker Notes

Streamlit app for tracking guest speaker notes in the Golisano AI & Business program. Records each speaker's AI usage, key takeaways, and cross-speaker comparisons. Data stored locally as JSON with Excel export.

---

## Prerequisites

- Python 3.11+

---

## 1. Clone and create virtual environment

```powershell
git clone <your-repo-url>
cd sfe-speaker-notes
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

---

## 2. Run the app

```powershell
streamlit run app.py
```

App opens automatically in your browser at `http://localhost:8501`.

Data is saved to `sfe_data.json` in the project folder. This file is your database — back it up if you care about the notes.

---

## Stack summary

| Layer | Technology |
|---|---|
| UI | Streamlit |
| Storage | JSON file (`sfe_data.json`) |
| Export | Excel (openpyxl) |
