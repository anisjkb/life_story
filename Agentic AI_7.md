Sure, Md! Here's a simple HTML dashboard you can use as a local control panel to trigger different report endpoints in your FastAPI project. This works great for previewing and downloading reports without having to use raw URLs:

---

## 💻 `dashboard.html`

Save this file inside `fin_intelli/templates/dashboard.html`:

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8" />
  <title>FinIntelli Reports Dashboard</title>
  <style>
    body { font-family: sans-serif; padding: 40px; background-color: #f9f9f9; }
    h1 { color: #2c3e50; }
    .button {
      display: inline-block;
      margin: 10px;
      padding: 10px 20px;
      font-size: 1em;
      background-color: #3498db;
      color: white;
      border: none;
      border-radius: 4px;
      text-decoration: none;
    }
    .button:hover {
      background-color: #2980b9;
    }
    iframe {
      margin-top: 30px;
      width: 100%;
      height: 600px;
      border: 1px solid #ccc;
    }
  </style>
</head>
<body>
  <h1>📂 FinIntelli Reports Dashboard</h1>

  <a class="button" href="/run-agent" target="report_frame">Run Agent Report</a>
  <a class="button" href="/report-kyc" target="report_frame">KYC Report</a>
  <a class="button" href="/report-risk" target="report_frame">Risk Report</a>
  <a class="button" href="/report-trend" target="report_frame">Trend Report</a>

  <iframe name="report_frame"></iframe>
</body>
</html>
```

---

## 🔧 To Serve This Page from FastAPI

Update `routes.py` to serve static files:

```python
from fastapi.responses import HTMLResponse

@router.get("/")
def dashboard():
    with open("templates/dashboard.html", encoding="utf-8") as f:
        return HTMLResponse(content=f.read())
```

Visit:  
📊 [http://127.0.0.1:8000](http://127.0.0.1:8000)  
You’ll see a polished dashboard with clickable buttons that trigger each report and display the result in the embedded preview area.

---

Want to enhance it with toggles for date filtering or export buttons for CSV/XML formats too? This could evolve into a full reporting console 🤖📋
