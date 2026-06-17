# Ultimate Logistics Procedures Dashboard

A hosted procedures and checklists dashboard for Ultimate Logistics LLC office staff. Built as a single-page HTML app on GitHub Pages, integrated with Microsoft 365 / SharePoint via the Microsoft Graph API.

**Live:** https://mh-ultimateidaho.github.io/procedures-dashboard/

---

## What it does

- **62 procedure cards** organized by section (Daily & Routine, Checklists, Driver Onboarding, Payroll, Taxes, Compliance, etc.)
- **Driver event checklists** (New Driver, Authority Change, Truck Change, Driver Quits, etc.) with a context form — select a driver from a live dropdown and checklist items auto-fill with their name, authority, and truck numbers
- **Home screen** with pinned cards, recently opened cards, and an In Progress section for multi-day checklists
- **Checkboxes** on every checklist item with strikethrough — per-session, resets on next open
- **Edit any step or item** — hover a step or checklist item and click ✎ to edit the text, add a link, or change the chip label. Changes save to SharePoint and are visible to all users
- **Add and delete steps** on any card — click + Add step at the bottom of a card's step list, or ✕ to remove a step
- **Create new cards** — click + New card in the All Procedures view to build a custom card with steps and/or owner blocks (KJ does / Amie does / Your tasks / etc.)
- **SharePoint sync** — all customizations (edits, new steps, new cards) save to `dashboard-config.json` on SharePoint so every signed-in user sees the same version
- **Live driver dropdown** — pulls current drivers directly from `Current Trucks & Drivers.xlsx` on SharePoint at load time, no manual updates needed
- **Folder links** — selecting a driver shows direct links to their SharePoint folder and truck folder
- **File path links** throughout — procedure steps link directly to relevant SharePoint folders and Excel files (permit sheets, Current Quartix, etc.), all opening in browser

---

## Authentication

Uses **MSAL.js v2** (`@azure/msal-browser@2.38.3`) with the `loginRedirect` flow.

- **App registration:** Procedures Dashboard (`2688e58a-1841-4c5b-8630-f92ff88238cc`)
- **Tenant:** `2c6b3d13-b98c-4605-8fcc-d8eed3579136`
- **Redirect URI:** `https://mh-ultimateidaho.github.io/procedures-dashboard/`
- **Scopes:** `Files.Read`, `Files.ReadWrite`, `Sites.Read.All`

On first open, users are redirected to Microsoft login. After signing in, tokens are cached in localStorage and subsequent loads are silent.

---

## SharePoint integration

| What | Location |
|---|---|
| Live driver list | `Driver Documents/Current Trucks & Drivers.xlsx` |
| Dashboard config (edits, custom cards, step changes) | `Office Documents/dashboard-config.json` |

The config file is created automatically on first save. It stores all customizations in JSON format and is loaded after auth on every page open.

---

## Deployment

Push `index.html` to the `main` branch. GitHub Pages serves it automatically from the repo root.

No build step, no dependencies to install — everything is a single self-contained HTML file.

---

## Azure App Registration

Configured in **Entra ID** under the `ultimatecorp.onmicrosoft.com` tenant.

Required API permissions (delegated):
- `Files.Read`
- `Files.ReadWrite`
- `Sites.Read.All`
- `User.Read` (default)

The redirect URI `https://mh-ultimateidaho.github.io/procedures-dashboard/` must be registered as a **Single-page application** platform URI.

---

## Related tools

- [Dispatch Doc Lookup](https://mh-ultimateidaho.github.io/doc-lookup/) — search driver, tractor, trailer, and CA TRU certificate folders
- [Office Doc Lookup](https://mh-ultimateidaho.github.io/doc-lookup-office/) — search office documents with category filtering
