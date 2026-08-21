# Ultimate Logistics Procedures Dashboard

A hosted procedures, checklist, Fleet Master, document-generation, expirations, and insurance-tracking dashboard for Ultimate Logistics LLC office staff. Built as a single-page HTML app on GitHub Pages and integrated with Microsoft 365 / SharePoint through the Microsoft Graph API.

**Live:** https://mh-ultimateidaho.github.io/procedures-dashboard/

---

## What it does

### Procedures and checklist workflow

- **Procedure cards** organized by section, including Daily & Routine, Checklists, Driver Onboarding, Payroll, Taxes, Compliance, and related office workflows.
- **Driver event checklists** for workflows like New Driver, Authority Change, Truck Change, Temporary Truck Change, Driver Quits, and similar multi-step processes.
- **Driver context forms** on driver-event cards pull from Fleet Master data and can auto-fill checklist items with driver name, authority/company, DOT number, current truck, and new truck.
- **Saved checklist sessions** let users save in-progress cards for multi-day workflows.
- **Generate from checklist cards** lets users create related documents directly from the checklist context, such as cover pages, lease terminations, and Word driver package documents.
- **Checkboxes** on checklist items with strikethrough for completed steps.
- **Editable card content** allows office users to edit steps/items, links, chip labels, sections, and owner blocks.
- **Custom cards** can be created from the dashboard for new procedures or one-off workflows.
- **Personal pins and recents** help each signed-in user keep their own frequently used cards handy.
- **Shared In Progress section** shows saved checklist sessions available to the team.

### Fleet Master integration

- Pulls live data from the configured **Fleet Master Sheet** on SharePoint.
- Uses Fleet Master tabs such as **Drivers**, **Authorities**, **Trucks**, **Trailers**, and **Assignments** for driver, authority, equipment, expiration, and document-generation data.
- Uses the Fleet Master **Status** dropdown to decide which drivers and authorities should be available in the dashboard.
- Included statuses:
  - `Current`
  - `Returning`
  - `Potential`
  - `Pending`
  - `New`
  - `Temp`
- Excluded statuses:
  - `Quit`
  - `Fired`
  - `Old`
- Ignores Fleet Master helper rows such as `NEXT ID` placeholder rows so generated IDs do not appear as real drivers or authorities.
- Caches the most recent Fleet Master data locally so the dashboard can open quickly with remembered data, then quietly refreshes live data when Microsoft/SharePoint finishes connecting.
- Hidden columns in Fleet Master are okay as long as the tab names and column headers remain intact.

### Fleet Info

- **Fleet Info** provides searchable snapshots for drivers, trucks, and authorities.
- Driver view can show CDL, CDL state, CDL expiration, medical expiration, SSN last 4, DOB, cell, email, address, current truck, and current authority when those fields exist in Fleet Master.
- Sensitive details like DOB and SSN last 4 are hidden behind reveal controls.
- Truck view shows unit details such as year, make/type, VIN, plate, color, assigned driver, and authority.
- Authority view shows company details such as DOT, MC, EIN, insurance expiration, policy details, and related drivers/equipment where available.
- Includes copy helpers and generation shortcuts for insurance/document workflows.

### Document generation

The dashboard includes a **Generate** menu for creating recurring office documents.

Supported generation tools:

- **Cover Page** PDF
- **Lease Termination** PDF
- **Driver Package / Word documents**
- Individual Word templates, including:
  - Commercial Package 60-40
  - Idaho LLC Operating Agreement
  - Two Page Lease
  - Tractor Docs Only
  - Driver Authority Package without Lease

Document generation can prefill from Fleet Master and/or a checklist card context. Manual fields remain available for information that is not known from Fleet Master, such as lease dates, payment fields, full SSN, or other package-specific details.

Privacy notes:

- Full SSN is manual-only and is not saved to the shared dashboard data.
- DOB can be pulled from Fleet Master when available, but sensitive fields should be handled carefully.
- Generated Word documents are downloaded locally by the user.

### Expirations tracker

- Tracks expiration-related items from Fleet Master source tabs, not from the old manual Expirations tab.
- Pulls expiration dates from:
  - Drivers: CDL and Medical
  - Authorities: Insurance
  - Trucks: Annual Inspection
  - Trailers: Annual Inspection
- Views include:
  - Needs attention
  - All current items
  - Expired
  - Expiring — 3 days
  - Due soon — 15 days
  - Upcoming — 30 days
  - Has active notes
  - Needs review
  - Temp / exception
  - Archived notes
- **Needs attention** includes expired, expiring, due soon, upcoming, missing date, and needs review.
- Shared notes can be added to expiration items.
- Notes can be edited, archived, restored, deleted, or manually marked for review.
- Date changes can flag notes for review, but editing/deleting notes does not automatically clear review status.
- Sort options include expiration date, item, category, type, status, and direction.

### Insurance tracker

- Tracks authority insurance work in a recap-style table.
- Uses authority insurance expiration dates from Fleet Master automatically.
- Rows with **No insurance exp date** sort as oldest when using oldest/date sorting, which keeps new/pending insurance work near the top.
- Insurance tracker display columns:
  - Account
  - Status
  - Quotes Received
  - Key Note
  - Actions
- Structured quote entry includes:
  - Broker
  - Insurance company/carrier
  - Coverage or outcome
  - Dollar amount
  - Quote note
- Dollar amounts auto-format as currency where possible.
- Quote notes display in bold so they stand apart from the main quote details.
- Key notes have their own larger text area and quick dated-update helper.
- Views include options such as all authorities, working/shopping statuses, and **With key notes**.
- **Export Recap** exports the currently filtered/sorted insurance view in a landscape recap format similar to the working Word recap table.

### Shared sync and data storage

- Shared dashboard edits, custom cards, saved sessions, expiration notes, insurance tracking, and related dashboard data save to a shared JSON config file on SharePoint.
- Personal pins are stored per signed-in user.
- Recent cards remain local to the browser.
- Fleet Master data is read from SharePoint and cached locally for faster startup.
- Settings includes practical maintenance actions such as refreshing Fleet Master data, reloading shared edits, syncing changes, clearing pins, and running health checks.

---

## Authentication

Uses **MSAL.js v2** (`@azure/msal-browser@2.38.3`) with the `loginRedirect` flow.

- **App registration:** Procedures Dashboard (`2688e58a-1841-4c5b-8630-f92ff88238cc`)
- **Tenant:** `2c6b3d13-b98c-4605-8fcc-d8eed3579136`
- **Redirect URI:** `https://mh-ultimateidaho.github.io/procedures-dashboard/`
- **Scopes:** `Files.Read`, `Files.ReadWrite`, `Sites.Read.All`

On first open, users are redirected to Microsoft login. After signing in, tokens are cached in localStorage and later loads should usually reconnect silently.

---

## SharePoint integration

| What | Source / storage |
|---|---|
| Fleet Master data | Configured Fleet Master Sheet on SharePoint |
| Shared dashboard configuration | `procedures-dashboard-config.json` on SharePoint |
| Procedure edits and custom cards | Shared dashboard configuration JSON |
| Saved checklist sessions | Shared dashboard configuration JSON |
| Expiration notes and review flags | Shared dashboard configuration JSON |
| Insurance tracking notes/quotes/statuses | Shared dashboard configuration JSON |
| Personal pins | Shared per signed-in user in dashboard data |
| Recent cards | Local browser only |
| Remembered Fleet Master data | Local browser cache |

Fleet Master column headers matter more than column positions. Columns may be hidden in Excel without breaking the dashboard, but tab names and expected headers should not be deleted or renamed unless the dashboard parser is updated too.

---

## Fleet Master expectations

The dashboard expects these general Fleet Master patterns:

### Drivers tab

Useful headers include:

- Driver ID
- First
- Middle
- Last
- Suffix
- Status
- CDL #
- CDL State
- CDL Exp
- Medical Exp
- SSN Last 4
- DOB
- Cell
- Email
- Address

### Authorities tab

Useful headers include:

- Authority ID
- Name
- Status
- DOT #
- MC #
- Insurer/Agent
- Policy Type
- Policy Date
- Insurance Exp
- EIN

### Trucks / Trailers tabs

Useful headers include unit number, year, make/type, model, VIN, plate, color, annual inspection, and in-service/status-style fields when available.

### Status handling

The dashboard includes rows with these statuses:

```text
Current
Returning
Potential
Pending
New
Temp
```

The dashboard excludes rows with these statuses:

```text
Quit
Fired
Old
```

Blank statuses may still appear in limited places if the row has other meaningful data, such as a real future expiration date or saved dashboard tracking data.

---

## Deployment

Push `index.html` to the `main` branch. GitHub Pages serves it automatically from the repo root.

No build step is required. The dashboard is a single self-contained HTML file with browser-loaded dependencies.

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

- [Dispatch Doc Lookup](https://mh-ultimateidaho.github.io/doc-lookup/) — search driver, tractor, trailer, and CA TRU certificate folders.
- [Office Doc Lookup](https://mh-ultimateidaho.github.io/doc-lookup-office/) — search office documents with category filtering.

---

## Maintenance notes

- Refresh Fleet Master data after changing Fleet Master tab names, headers, statuses, or important date fields.
- Avoid renaming drivers/authorities unless necessary; notes attach best when names stay stable.
- If a row disappears unexpectedly, check the Fleet Master `Status` value first.
- If old cached data seems wrong, use Settings → Refresh Fleet Master data.
- Do not use fake medical or insurance dates just to force a row into the dashboard. Use the proper `Status` value instead, such as `Returning`, `Potential`, `Pending`, `New`, or `Temp`.

---

## Planned updates

Ideas to consider later:

- More Word templates under Generate.
- Better admin/role controls for sensitive fields.
- Optional note-matching tools for renamed drivers/authorities.
- More detailed Fleet Master health checks.
