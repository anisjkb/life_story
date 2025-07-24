Awesome — let’s evolve your dashboard from a passive checklist into a dynamic control center that actually reflects **live status, test history**, and **report generation logs** 💡📊

---

## 🎯 Goal: Dynamic Report Tracker

You’ll get:
- ✅ Automatic checkmarks for completed agent modules
- 📅 Timestamps showing last run or update
- 📂 Links to generated reports (PDF, CSV, HTML)
- 📦 Optional warnings for incomplete files (e.g., missing docstring/test)

---

## 🧱 Strategy

We’ll build a backend service that:
1. Checks file presence and modification timestamps
2. Validates whether report outputs (PDFs/CSVs) exist
3. Optionally parses Python files for missing structure
4. Renders an HTML table with green/red/yellow indicators

---

## 🔧 Step 1: Create `status_tracker.py` in `fin_intelli/src/utils/`

```python
import os
from datetime import datetime

PROJECT_ROOT = os.path.dirname(os.path.dirname(__file__))

MODULES = [
    {"name": "Trend Agent", "path": "src/agents/trend_agent.py"},
    {"name": "Pipeline Agent", "path": "src/agents/pipeline.py"},
    {"name": "EDA Module", "path": "src/eda.py"},
    {"name": "Report Generator", "path": "src/report_generator.py"},
]

REPORTS = [
    {"name": "Trend PDF", "path": "outputs/reports/trend_report.pdf"},
    {"name": "Trend CSV", "path": "outputs/reports/trend_report.csv"},
    {"name": "KYC Report", "path": "outputs/reports/kyc_report.pdf"},
]

def get_file_status(relative_path):
    abs_path = os.path.join(PROJECT_ROOT, relative_path)
    exists = os.path.exists(abs_path)
    timestamp = os.path.getmtime(abs_path) if exists else None
    return exists, timestamp

def build_status():
    rows = []
    for item in MODULES + REPORTS:
        exists, timestamp = get_file_status(item["path"])
        last = datetime.fromtimestamp(timestamp).strftime("%Y-%m-%d %H:%M") if timestamp else "❌"
        status = "✅" if exists else "❌"
        rows.append({
            "name": item["name"],
            "status": status,
            "last_updated": last,
            "link": item["path"].replace("\\", "/") if exists else None
        })
    return rows
```

---

## 🌐 Step 2: Render to HTML in FastAPI

Add this endpoint to `routes.py`:

```python
from src.utils.status_tracker import build_status
from fastapi.responses import HTMLResponse

@router.get("/status")
def show_status():
    rows = build_status()
    html = "<html><body><h1>📋 FinIntelli Status Tracker</h1><table border='1' cellspacing='0' cellpadding='6'>"
    html += "<tr><th>Name</th><th>Status</th><th>Last Updated</th><th>Link</th></tr>"
    for r in rows:
        link = f"<a href='../{r['link']}' target='_blank'>Open</a>" if r['link'] else "-"
        html += f"<tr><td>{r['name']}</td><td>{r['status']}</td><td>{r['last_updated']}</td><td>{link}</td></tr>"
    html += "</table></body></html>"
    return HTMLResponse(content=html)
```

Access your tracker here:  
🌐 [http://127.0.0.1:8000/status](http://127.0.0.1:8000/status)

---

## 🚀 What’s Next?

Want me to add:
- Python file parsing to warn if any module lacks `def`, `docstring`, or `__main__` entry?
- Test result badges if `pytest` logs show pass/fail?
- Export status as a JSON or CSV summary for report audits?

You’re building not just an app — you’re shaping an ecosystem 🧠📂✅
