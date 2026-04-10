# Setup Guide — PaperVault

Local-first document intake, OCR, archive, and search system. Scan or photograph paper documents → OCR via Tesseract → SQLite database → human review → permanent searchable record. Business cards become contacts; receipts can be exported to Inventory.

---

## Prerequisites

- Python 3.11+
- [Tesseract OCR](https://github.com/UB-Mannheim/tesseract/wiki) — Windows binary installer
- (Optional) Anthropic API key — for the auto-watcher document classifier

---

## 1. Install Tesseract

Download and run the installer from:
https://github.com/UB-Mannheim/tesseract/wiki

Install to the default path (`C:\Program Files\Tesseract-OCR\`). The app auto-detects it there on Windows. If you install elsewhere, update the path in `config/papervault.yaml`.

---

## 2. Install the package

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -e .
```

The `-e` flag installs in editable mode so the `vault` CLI command is available.

---

## 3. Initialize the database

```powershell
python scripts/init_db.py
vault init
```

This creates:
- `db/papervault.db` — SQLite database
- `intake_inbox/` — drop documents here for processing
- `intake_archive/` — processed originals are moved here

---

## 4. Configure (optional)

Edit `config/papervault.yaml` to adjust:
- `database.path` — where the SQLite file lives
- `intake.inbox_dir` / `intake.archive_dir` — intake folder locations
- `watcher.watch_dir` — folder to auto-monitor (e.g. an iCloud or OneDrive inbox)
- `ai.enabled` — set to `true` only after benchmarking (see below)

---

## 5. Basic usage

```powershell
# Add a single document
vault intake path/to/scan.jpg

# Batch intake from a folder
vault intake path/to/inbox/

# Review and confirm documents interactively
vault review

# Search across all archived documents
vault search "receipt target"

# Database summary
vault stats
```

---

## Auto-watcher (optional)

The watcher monitors a folder and automatically intakes new documents:

```powershell
vault watch
```

Configure the watch directory in `config/papervault.yaml` under `watcher.watch_dir`.

The watcher can optionally classify documents using Claude Haiku. To enable, add your Anthropic API key as an environment variable:

```powershell
$env:ANTHROPIC_API_KEY = "sk-ant-..."
```

And set the classifier model in `papervault.yaml`:
```yaml
watcher:
  classifier:
    provider: anthropic
    model: "claude-haiku-4-5-20251001"
```

---

## Local AI extraction (optional, not yet implemented)

AI-powered field extraction (Phase 7) is not yet built. The infrastructure is in place — set `ai.enabled: true` in `papervault.yaml` after:

1. Running the benchmark: `python scripts/benchmark_ai.py`
2. Confirming ≥75% accuracy on real labeled examples
3. Verifying your local model is running (`ollama serve`)

AI output always goes to `extracted_fields` with `status: pending_review`. It never auto-commits — human review is always required.

---

## Supported file types

- `.jpg` / `.jpeg` / `.png` — images (best quality)
- `.pdf` — text-based PDFs only; image-only PDFs are not supported

---

## Stack summary

| Layer | Technology |
|-------|-----------|
| CLI | Click (Python) |
| OCR | Tesseract |
| Database | SQLite (local file) |
| Image processing | Pillow, PyMuPDF |
| Optional AI | Anthropic Claude Haiku / Ollama |
| Export | Excel (openpyxl) |
