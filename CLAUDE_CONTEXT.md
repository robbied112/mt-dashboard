# Missing Thorn BI Dashboard - Project Context

## Overview

Executive-level business intelligence dashboard for Missing Thorn, a CPG wine brand selling through distributors. Single-file HTML application deployed on GitHub Pages with Firebase Firestore for CRM data persistence.

## Architecture

### Single-File HTML Dashboard
- **File**: `index.html` (~337 KB, ~3,500 lines)
- **Sections**:
  - Lines 1-298: HTML head, external CDN libraries, embedded CSS
  - Lines 299-660: HTML body structure (header, nav tabs, filter bar, tab content areas)
  - Lines 660-3018: JavaScript application logic
  - Lines 673-1776: `__DATA_START__` / `__DATA_END__` markers (injected data)
  - Lines 3018-3500: Firebase config, AccountStore, CRM panel, CRM manager tab

### External Dependencies (CDN)
- **Google Fonts**: Catamaran
- **Chart.js** v4.4.1: All charts and visualizations
- **SheetJS (XLSX)** v0.18.5: Excel export functionality
- **Firebase** v10.8.0: Firestore for CRM data persistence

### Build Pipeline
Python scripts (not in this repo) read 6+ Excel source files:
- VIP distributor portal exports (depletions, inventory, placements)
- QuickBooks exports (orders, revenue)

Scripts generate JavaScript data objects and inject them between `__DATA_START__` / `__DATA_END__` markers in the HTML template. The injected data objects are:

| Object | Description |
|--------|-------------|
| `stateNames` | State abbreviation → full name mapping (12 states) |
| `regionMap` | State → region mapping (East/West) |
| `distScorecard` | 16 distributors with 13-week metrics (CE, revenue, on-hand, DOH, consistency, health scores) |
| `inventoryData` | Inventory status by distributor with SKU-level breakdown |
| `placementData` | New/lost accounts tracking per distributor |
| `newAccounts` | 50+ new retail accounts with CE and SKU counts |
| `accountsTop` | 100+ top accounts ranked by performance with monthly data and trends |
| `reorderData` | 200+ accounts with purchase cycle analysis and SKU-level detail |
| `qbDistOrders` | QuickBooks order history per distributor |
| `warehouseInventory` | Classic and Contemporary wine inventory by location |
| `classicTracker` | Inventory burn rate tracking with historical data |
| `buildDate` | Build timestamp |
| `dataThrough` | Data freshness date |

### Firebase / Firestore

**Project**: `mt-dashboard-dab8e`

**Collections**:
- `accounts/{accountId}` — Document per account (ID = sanitized account name)
  - Fields: `tags` (array), `status` (string), `followUp` (date), `nextAction` (string)
  - Subcollection: `notes/{noteId}` — Notes with `text`, `author`, `ts`, `date`

**AccountStore** (lines ~3018-3087):
- `getNotes(acctId)` — Fetch notes (Firestore primary, localStorage fallback)
- `addNote(acctId, text, author)` — Add timestamped note
- `getMeta(acctId)` — Get tags, status, followUp, nextAction
- `setMeta(acctId, meta)` — Persist metadata with merge
- Dual-mode: Firebase if available, localStorage fallback with key `mt_acct_{sanitizedId}`

### Authentication
- **None**. No login or access control.
- Rep name prompted on first note and stored in `localStorage` as `mt_rep_name`
- Firebase API keys are hardcoded (standard for client-side apps, secured by Firestore rules)

## Dashboard Tabs

1. **Executive Overview** — Snapshot KPIs, 13W depletion trend, top distributors chart, alerts
2. **Revenue & Sales** — 2026 YTD revenue by channel vs $2.85M budget, monthly breakdown, SKU revenue
3. **Depletions** — 13-week analysis, distributor scorecard (CE, revenue, YoY%, 4W trend, DOH, health score)
4. **Distributor Detail** — Per-distributor SKU-level 13W performance
5. **Inventory Health** — Warehouse panel, status distribution, on-hand vs DOH scatter, projected reorder quantities
6. **Account Analytics** — 4-month rolling period, trend distribution, risk overview, searchable account table
7. **Placements** — New vs lost accounts tracker by distributor
8. **Reorder Forecast** — Priority reorder list, purchase cycle analysis
9. **Account CRM** — Manager view: tag filter, state filter, sort options, KPI cards, alerts (stale VIPs, at-risk, overdue follow-ups)
10. **Key / Legend** — Metric definitions and abbreviations

## CRM Features

### Tags
8 predefined: VIP, At Risk, Hot Lead, Needs Visit, Seasonal, Chain, Independent, Follow Up

### Notes
- Textarea entry with author attribution
- Timestamped, sorted descending
- Stored in Firestore subcollection

### Next Actions
Dropdown options: Call, Email, Visit, Send Samples, Follow Up, Reorder Check
- Paired with follow-up date field
- Visible in CRM manager report

### CRM Manager Report
- Filterable by tag, state
- Sortable by priority, days since order, CE, tags
- Shows: account, distributor, state, tags, 4-month CE, last order, days since order, next action, follow-up date, note count
- Alert cards for stale VIPs (>30 days), at-risk accounts, overdue follow-ups

## Key Business Context

- **Product**: Wine (5 types: Still Red, Still White, Still Rose, Sparkling White, Sparkling Rose)
- **Distribution**: Through 16+ distributors across 12 states in East/West regions
- **Key Metrics**: CE (Case Equivalents), DOH (Days on Hand), Sell-Through Rate, Consistency (% of weeks with sales), Health Score
- **Stages**: Launch, Growth, Mature (per distributor)
- **Users**: CRO (executive view) + 1 rep (rep dashboard), potentially growing to 4-6 reps

## Deployment
- GitHub Pages (static hosting)
- Repo: `robbied112/mt-dashboard`
- Branch: `main` (deployed)

## Companion Repository
- **mt-rep-dashboard** (`robbied112/mt-rep-dashboard`): Rep-facing version with performance overview, depletions, distributor health, inventory, account insights, opportunities, reorder forecast, and "My Action Items" tab
- Shares the same Firebase project (`mt-dashboard-dab8e`) for CRM data
