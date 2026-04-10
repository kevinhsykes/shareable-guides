# Setup Guide — local-ai-lab

Reusable local AI package (`local_ai_core`) and experiment workspace. Provides provider abstraction, task routing, and audit logging for consumer projects. Built around Ollama running local models on a GPU.

---

## Prerequisites

- Python 3.11+
- [Ollama](https://ollama.com) — local model runtime
- A GPU with enough VRAM for your chosen model (or CPU with patience)

---

## 1. Install Ollama

Download from [ollama.com](https://ollama.com) and install. Ollama runs as a background service on Windows after install.

Verify it's running:
```bash
curl http://localhost:11434/api/version
```

---

## 2. Pull a model

```bash
ollama pull gemma4          # 8B, ~9.6 GB — recommended for tasks
ollama pull nomic-embed-text  # embeddings model (optional)
ollama list                 # confirm what's installed
```

Choose a model that fits your VRAM. Common options:
- **4 GB VRAM:** `gemma2:2b`, `phi3:mini`
- **8 GB VRAM:** `llama3.2`, `mistral`
- **16 GB VRAM:** `gemma4` (8B), `llama3.1:8b`

---

## 3. Install the package

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -e .
```

The `-e` flag makes `local_ai_core` importable from other projects on the same machine.

To use it from another project:
```powershell
pip install -e C:\path\to\local-ai-lab
```

---

## 4. Configure

Copy the example config and edit it:
```powershell
copy configs\local_ai_example.yaml configs\my_project.yaml
```

Key settings in the YAML:
```yaml
enabled: true               # master switch

providers:
  ollama:
    base_url: "http://localhost:11434/v1"
    default_model: "gemma4"  # must match: ollama list

tasks:
  your_task_name:
    enabled: true
    provider: ollama
    model: "gemma4"
    confidence_threshold: 0.80
```

---

## 5. Using `local_ai_core` in another project

```python
from local_ai_core import TaskRouter, TaskRequest

router = TaskRouter(
    config_path="path/to/your_config.yaml",
    task_modules={"your_task": your_task_module}
)

result = router.run(TaskRequest(task="your_task", inputs={"text": "..."}))

if result.needs_review:
    # confidence below threshold — send to human review
    pass
```

Each task module must expose:
- `build_prompt(inputs: dict) -> str`
- `validate_output(raw: str) -> dict`  (must include a `confidence` float 0–1)

---

## 6. Audit log

All task runs are logged to `logs/local_ai_audit.jsonl` (gitignored). Enable/disable in your config:
```yaml
audit:
  enabled: true
  log_path: "logs/local_ai_audit.jsonl"
```

---

## Using LM Studio instead of Ollama

LM Studio exposes the same OpenAI-compatible API. Uncomment the `lmstudio` provider in your config:
```yaml
providers:
  lmstudio:
    base_url: "http://localhost:1234/v1"
    default_model: "your-loaded-model-name"
    timeout_s: 30
```

---

## Stack summary

| Layer | Technology |
|-------|-----------|
| Package | Python (setuptools, editable install) |
| Runtime | Ollama or LM Studio |
| API | OpenAI-compatible (`/v1/chat/completions`) |
| Config | YAML |
| Audit | JSONL log file |
