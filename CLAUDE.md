# NexGen FTF — AI Report Generation Guidelines

This repo stores AI-generated HTML dashboards and reports for NexGen Surveying FTF operations.

## Critical Rule: Self-Contained HTML Files

**Every HTML dashboard generated must be fully self-contained — no external file dependencies.**

### Chart.js

- Do NOT use `<script src="../chart.min.js">` or any relative/CDN path for Chart.js.
- Instead, inline the full Chart.js library as a `<script>` block at the top of `<head>`.
- The library content lives at `Reports/chart.min.js` — read it and paste it inline.
- This ensures charts render on any device, offline, when shared via email, or downloaded individually.

Example of what to do:
```html
<head>
<script>
/* Chart.js v4.4.0 full minified content here */
</script>
...
```

Example of what NOT to do:
```html
<script src="../chart.min.js"></script>          <!-- breaks when file is shared -->
<script src="https://cdn.example.com/..."></script> <!-- breaks offline -->
```

### Logo

- Do NOT use `<img src="../nexgen_logo.png">` without a fallback.
- Always include an `onerror` handler that shows text when the image is missing:
```html
<img src="../nexgen_logo.png" alt="NexGen"
     onerror="this.style.display='none';this.nextElementSibling.style.display='block'">
<span style="display:none">NexGen</span>
```

## HTML Standards for Every Report

- `<meta charset="UTF-8">` — always first in `<head>`
- `<meta name="viewport" content="width=device-width, initial-scale=1.0">` — required for mobile
- `<html lang="en">` — always set the language attribute
- Responsive breakpoint at `700px` minimum
- All CSS embedded in a single `<style>` block — no external stylesheets
- All data embedded as JavaScript variables — no external data fetches
- System font stack only: `font-family: 'Segoe UI', Arial, sans-serif`

## Report Folder Structure

New reports go in `Reports/Report YYYY-MM-DD [HH-MM]/` with:
- `FTF_DASHBOARD_*.html` — self-contained interactive dashboard
- `FTF_MASTER_REPORT_*.xlsx` — full Excel report
- `FTF_MASTER_REPORT_*_SUMMARY.pdf` — PDF summary
- `REPORT_BRIEF.txt` — human-readable summary
- `REPORT_PROMPT.txt` — SQL queries and prompt used to generate the report

## Why Self-Contained Matters

These dashboards are shared across teams, emailed, and opened on a wide variety of devices and network conditions. A dashboard that depends on files at relative paths or CDN URLs will silently break whenever:
- Only the HTML file is shared (most common case)
- The recipient is on a restricted network
- The CDN is blocked or unavailable in their region
- The folder structure differs on their machine
