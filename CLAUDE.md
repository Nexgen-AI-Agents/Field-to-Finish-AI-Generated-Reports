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

---

## Database Schema — nexgen_ftf_db (AWS RDS MySQL)

Every report pulls from these tables. Know them before querying.

### Core Operations Tables
| Table | Description |
|---|---|
| `ng_orders` | **Primary orders table** — belongs to customers; represents all survey/job work to be done. Central table for every report. |
| `ng_statuses` | Lookup/reference table defining every possible order status (e.g. Pending, In-Field, In-Drafting, Delivered, Cancelled, Go-Back). |
| `ng_job_types` | Lookup table defining job type classifications (ALTA/NSPS, Commercial, Qualia, Trueline, Metes & Bounds, Lot & Block, Sectional, Update, Townhouse, etc.). |
| `ng_log_trackflow` | **Critical for Status Aging reports** — logs every status transition an order goes through, with timestamps. Source of all workflow transition intelligence. |
| `order_go_back_history` | Logs when an order is sent back to a previous status and the reason why. Source of go-back root cause analysis. |
| `crew_job_completed` | Tracks jobs that field crew members have finished — links crews to completed work. |

### People & Accounts Tables
| Table | Description |
|---|---|
| `ng_accounts` | All staff members and accounts (customers/clients) — belongs to companies or individuals; stores profile and contact data. |
| `ng_acc_internal_info` | Internal HR staff (crew, drafters, coordinators) — stores permissions, role details, and internal account info. |
| `ng_company` | Company records — stores company names and info that customers/accounts belong to. |
| `ng_contact_info` | Contact information — stores contact details associated with accounts or orders. |
| `ng_additional_recipients` | Additional email recipients on orders — extra contacts who receive order-related emails. |

### Financial Tables
| Table | Description |
|---|---|
| `ng_payments` | Payment transactions — linked to orders and clients; tracks amounts paid. |
| `ng_payments_check_detail` | Check payment breakdowns — detailed line items for check-based payments. |
| `books_highlights` | Financial highlights and summaries — aggregated financial data for the books/accounting module. |

### Workforce & Time Tracking Tables
| Table | Description |
|---|---|
| `user_attendance` | Employee clock-in/out records — tracks when field and office staff start and end their workday. |
| `manual_time_entries` | Manually entered time records — used when clock-in/out is adjusted or entered by hand. |
| `ng_crew_hours` | Crew work hours — stores hour logs attributed to crew members per job or day. |
| `user_location_tracking` | GPS location tracking — logs the real-time location of field crew members during work hours. |
| `fieldpack_jobs` | FieldPack integration job data — stores job data synced from or sent to the FieldPack system. |

### Notifications & Communications Tables
| Table | Description |
|---|---|
| `ng_email_log` | Email delivery logs — records every email sent out of the system for auditing and tracking. |
| `autogenerated_emails` | Auto-generated email templates — stores email content automatically created for orders or events. |
| `ng_job_notification_text` | Notification message templates — stores text used for job-related push/SMS/email notifications. |
| `push_notifications_device_detail` | Push notification device registrations — stores device tokens for FCM push notifications to mobile devices. |

### Calendar & Events Tables
| Table | Description |
|---|---|
| `user_calendar_sync` | Calendar synchronization status — tracks Google/Microsoft calendar sync state per user. |
| `user_events` | Calendar events — stores meetings and events tied to users. |
| `user_event_accounts` | Event-to-account associations — links calendar events to relevant staff or client accounts. |

### Audit & Access Control Tables
| Table | Description |
|---|---|
| `ng_log_hr_activity` | HR activity audit logs — records HR-related actions taken by users for accountability. |
| `ng_user_activity_log` | General user action logs — records all user actions across the platform for audit purposes. |
| `ng_module_permission` | Role-based module access control — defines which roles have read/write/delete access to each module. |
| `reports_permissions` | Reporting module access control — defines which users or roles can access specific reports. |

### Configuration & Reference Tables
| Table | Description |
|---|---|
| `county_url_list` | County-specific URL mappings — maps county names to relevant external URLs used in order processing. |
| `report_notification_scheduler_setting` | Daily report notification scheduling config — controls when and to whom automated report notifications are sent. |

### Key Relationships for Report Queries
```
ng_orders
  ├── ng_accounts        (customer who placed the order)
  ├── ng_company         (company the customer belongs to)
  ├── ng_job_types       (type of survey job)
  ├── ng_statuses        (current status)
  ├── ng_log_trackflow   (full status history + timestamps → Status Aging)
  ├── order_go_back_history (go-back reasons)
  ├── ng_payments        (payments received)
  ├── crew_job_completed (which crew completed it)
  └── ng_email_log       (emails sent for this order)

ng_acc_internal_info
  ├── ng_accounts        (the staff member's profile)
  ├── user_attendance    (their clock-in/out records)
  ├── ng_crew_hours      (their job hours)
  └── user_location_tracking (their GPS during work)
```

### Most Important Tables for Each Report Type
- **Operations / KPI reports** → `ng_orders`, `ng_statuses`, `ng_job_types`, `ng_accounts`
- **Status Aging / Workflow Transition** → `ng_log_trackflow`, `ng_orders`, `ng_statuses`
- **Go-Back Analysis** → `order_go_back_history`, `ng_orders`, `ng_acc_internal_info`
- **Crew Performance** → `crew_job_completed`, `ng_crew_hours`, `ng_acc_internal_info`, `order_go_back_history`
- **Financial / Revenue** → `ng_orders`, `ng_payments`, `ng_payments_check_detail`, `books_highlights`
- **Client Intelligence** → `ng_accounts`, `ng_company`, `ng_orders`, `ng_payments`
- **Workforce / HR** → `ng_acc_internal_info`, `user_attendance`, `manual_time_entries`, `ng_crew_hours`
- **Coordinator Performance** → `ng_acc_internal_info`, `ng_orders`, `ng_log_trackflow`
