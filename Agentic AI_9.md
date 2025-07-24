Perfect, Md! Let's update `trend_agent.py` so it becomes truly agentic — supporting:

✅ Dynamic date range  
✅ Output format selection: `pdf`, `csv`, or `html`  
✅ Ready for FastAPI endpoint parameters

---

## 📂 `src/agents/trend_agent.py` (Enhanced Version)

```python
import pandas as pd
import matplotlib.pyplot as plt
import os
from src.data_loader import load_data
from src.report_generator import render_pdf

def run_trend_agent(start=None, end=None, format="pdf"):
    df = load_data()

    # Handle date filtering
    df["tran_date"] = pd.to_datetime(df["tran_date"])
    if start:
        df = df[df["tran_date"] >= pd.to_datetime(start)]
    if end:
        df = df[df["tran_date"] <= pd.to_datetime(end)]

    # Generate monthly trend
    df["month"] = df["tran_date"].dt.to_period("M")
    monthly_trend = df.groupby("month")["amount"].sum().sort_index()

    plot_path = "outputs/plots/trend.png"
    os.makedirs(os.path.dirname(plot_path), exist_ok=True)

    if format == "csv":
        csv_path = "outputs/reports/trend_report.csv"
        monthly_trend.to_csv(csv_path, index=True)
        return csv_path

    elif format == "html":
        html_path = "outputs/reports/trend_report.html"
        html = f"""
        <html><body>
        <h1>📅 Transaction Trend Report</h1>
        <p>Date Range: {start or 'N/A'} to {end or 'N/A'}</p>
        <pre>{monthly_trend.to_string()}</pre>
        </body></html>
        """
        with open(html_path, "w", encoding="utf-8") as f:
            f.write(html)
        return html_path

    elif format == "pdf":
        # Generate plot for PDF
        monthly_trend.plot(kind="bar", figsize=(10, 6), title="Monthly Transaction Volume")
        plt.ylabel("Total Amount")
        plt.xlabel("Month")
        plt.xticks(rotation=45)
        plt.tight_layout()
        plt.savefig(plot_path)
        plt.close()

        html_path = "outputs/reports/trend_report.html"
        pdf_path = "outputs/reports/trend_report.pdf"
        html = f"""
        <html><body>
        <h1>📅 Transaction Trend Report</h1>
        <p>Date Range: {start or 'N/A'} to {end or 'N/A'}</p>
        <img src="{plot_path}" width="600">
        </body></html>
        """
        with open(html_path, "w", encoding="utf-8") as f:
            f.write(html)
        render_pdf(html_path, pdf_path)
        return pdf_path
```

---

## 🌐 Update `routes.py` to Accept Parameters

```python
from fastapi import Query
from src.agents.trend_agent import run_trend_agent
from fastapi.responses import FileResponse

@router.get("/report-trend")
def report_trend(
    start: str = Query(default=None),
    end: str = Query(default=None),
    format: str = Query(default="pdf")
):
    file_path = run_trend_agent(start=start, end=end, format=format)
    media_types = {
        "pdf": "application/pdf",
        "csv": "text/csv",
        "html": "text/html"
    }
    filename = os.path.basename(file_path)
    return FileResponse(file_path, media_type=media_types[format], filename=filename)
```

---

Now your dashboard selections — date range + format — will dynamically trigger the right version and deliver the correct file 🙌

Would you like help extending this logic to other agents too (e.g. `/report-risk`, `/report-kyc`), or build a log dashboard that shows which reports were generated when? You're paving the way toward a full fintech insights portal 🧾📂🪄
