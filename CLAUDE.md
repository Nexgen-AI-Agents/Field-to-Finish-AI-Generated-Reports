# NexGen FTF — AI Report Generation Guidelines

This repo stores AI-generated HTML dashboards and reports for NexGen Surveying FTF operations.

## Critical Rule: Self-Contained HTML Files

**Every HTML dashboard generated must be fully self-contained — no external file dependencies.**

### Chart.js
- Do NOT use `<script src="../chart.min.js">` or any CDN path.
- Read `Reports/chart.min.js` and paste its full content inline inside a `<script>` block.
- This ensures charts render on any device, offline, when shared via email or downloaded.

```html
<!-- CORRECT -->
<head>
<script>
/* Chart.js v4.4.0 full minified content here */
</script>

<!-- WRONG — breaks when file is shared -->
<script src="../chart.min.js"></script>
```

### Logo
- Always include `onerror` fallback on the logo `<img>` tag:
```html
<img src="../nexgen_logo.png" alt="NexGen"
     onerror="this.style.display='none';this.nextElementSibling.style.display='block'">
<span style="display:none">NexGen</span>
```

## HTML Standards for Every Report
- `<meta charset="UTF-8">` — always first in `<head>`
- `<meta name="viewport" content="width=device-width, initial-scale=1.0">` — required for mobile
- `<html lang="en">` — always set the language attribute
- Primary colors: `#0051c9` (blue) and `#d0b64f` (gold)
- All CSS embedded inline — no external stylesheets
- All data embedded as JavaScript variables — no external data fetches
- System font stack: `font-family: 'Segoe UI', Arial, sans-serif`
- Responsive breakpoint at minimum `700px`

## AI Intelligence System Standards
For the full AI Operations Intelligence System (`FTF_AI_INTELLIGENCE_SYSTEM.html`):
- Minimum 20 tabs covering all dashboard layers
- Global date range filter at the top
- Status Aging & Workflow Transition tabs required
- AI insight boxes on every tab (plain English explanations)
- Token cost transparency panel on System tab
- Color-coded severity: Red = critical, Amber = warning, Green = good

## Report Folder Structure
```
Reports/
  AI-Intelligence-System/
    FTF_AI_INTELLIGENCE_SYSTEM.html   ← 22-tab enterprise dashboard (self-contained)

  Report YYYY-MM-DD [HH-MM]/
    FTF_DASHBOARD_*.html              ← Individual dashboards (self-contained)
    FTF_MASTER_REPORT_*.xlsx
    FTF_MASTER_REPORT_*_SUMMARY.pdf
    REPORT_BRIEF.txt
    REPORT_PROMPT.txt

  chart.min.js                        ← Source for inlining (DO NOT link directly)
```

## Why Self-Contained Matters
These dashboards are shared across teams, emailed, and opened on a wide variety of
devices and network conditions. A dashboard that depends on relative paths or CDN URLs
will silently break when:
- Only the HTML file is shared (most common case)
- The recipient is on a restricted network
- The CDN is blocked or unavailable in their region
- The folder structure differs on their machine
