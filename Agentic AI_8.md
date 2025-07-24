Fantastic, Md — let’s enhance your HTML dashboard into a **functional reporting console** with dynamic features like date filtering, format selection (PDF/CSV), and contextual export options. Here’s a refreshed scaffold you can build on:

---

## 💻 Updated `dashboard.html` Template

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8" />
  <title>FinIntelli Reports Dashboard</title>
  <style>
    body { font-family: sans-serif; padding: 40px; background-color: #f9f9f9; }
    h1 { color: #2c3e50; }
    label { margin-right: 10px; font-weight: bold; }
    input, select { margin: 5px 0; padding: 6px; }
    .form-group { margin-bottom: 20px; }
    .button {
      display: inline-block;
      margin: 10px 10px 10px 0;
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

  <form id="reportForm" target="report_frame" method="get">
    <div class="form-group">
      <label for="reportType">Choose report type:</label>
      <select id="reportType" name="reportType">
        <option value="run-agent">Agent Report</option>
        <option value="report-kyc">KYC Report</option>
        <option value="report-risk">Risk Report</option>
        <option value="report-trend">Trend Report</option>
      </select>
    </div>

    <div class="form-group">
      <label for="start">From:</label>
      <input type="date" id="start" name="startDate">
      <label for="end">To:</label>
      <input type="date" id="end" name="endDate">
    </div>

    <div class="form-group">
      <label for="format">Format:</label>
      <select id="format" name="format">
        <option value="pdf">PDF</option>
        <option value="csv">CSV</option>
        <option value="html">HTML</option>
      </select>
    </div>

    <button type="submit" class="button">🔍 Generate Report</button>
  </form>

  <iframe name="report_frame"></iframe>

  <script>
    const form = document.getElementById("reportForm");
    form.onsubmit = function (e) {
      e.preventDefault();
      const type = document.getElementById("reportType").value;
      const start = document.getElementById("start").value;
      const end = document.getElementById("end").value;
      const format = document.getElementById("format").value;
      const targetUrl = `/${type}?start=${start}&end=${end}&format=${format}`;
      form.action = targetUrl;
      form.submit();
    };
  </script>
</body>
</html>
```

---

## 🧪 Next Steps on Backend

Your FastAPI route (e.g. `/report-trend`) should now accept:
- `start`: start date
- `end`: end date
- `format`: `pdf`, `html`, `csv`

I can help modify your `trend_agent.py` to slice the date range and return the correct format dynamically.

---

Ready for me to update `trend_agent.py` to support all three formats — slicing the data by date and exporting either PDF, HTML, or CSV based on user selection? Let’s make this console truly agentic ⚙️📊📤
