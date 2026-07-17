# Scimly

CSV-based user access review and SCIM-readiness dashboard. Paste (or load) a
user access export, run an analysis, and get a readiness score, a flagged
list of access-review problems, an estimated monthly seat-waste figure, and
an exportable Markdown report.

> **Edition:** Both live directory connectors included: Google Workspace and Microsoft Entra ID.

Built for: B2B SaaS founders, internal IT teams, compliance consultants, and
admin-tool buyers who need a lightweight access-review workflow before
investing in a larger identity/admin platform.

## What it does

- Parses a CSV of user access records entirely in the browser — a pasted or
  dropped CSV never leaves your machine. (Google Workspace and Microsoft Entra ID sync, described below, are separate, optional paths that do talk to those providers' APIs.)
- **Generate Mock Data** button produces a fresh, randomized 500-row CSV on
  every click — no real data required to see the tool work at realistic
  scale. The generated data deliberately includes messy edge cases (blank
  or unparseable dates, blank MFA fields, role values with stray
  whitespace, non-numeric/negative costs, missing SCIM ID, department, or
  manager, and a few duplicate emails), so a reviewer can see the flagging
  and scoring logic handle imperfect input, not just clean demo rows.
- Flags:
  - **Stale admin / stale user** — no login within the configurable
    threshold (default 90 days)
  - **Admin no MFA** — an Admin-role account without MFA enabled
  - **Missing SCIM ID** — no `scim_external_id`, a sign the account isn't
    provisioned through SCIM/SSO
  - **Missing department** / **Missing manager** — incomplete directory data
  - **Paid inactive user** — an inactive account still carrying a nonzero
    monthly license cost
- Scores each user 0–100 for risk, and rolls that up into a 0–100%
  workspace **readiness** score.
- Shows a low/medium/high risk-distribution bar across the workspace.
- Estimates monthly and annualized license waste from paid, inactive seats.
- Generates a Markdown access-review report you can copy or download, or
  export the full analyzed table as a CSV.

## Running it

This is a single static HTML file with no build step and no server.

1. Open `index.html` in any modern browser (double-click it, or serve the
   folder with any static file host).
2. Click **Load Demo Data** for a small 14-row sample, **Generate Mock Data**
   for a randomized 500-row messy dataset, **Connect Google Workspace** or **Connect Microsoft Entra ID**, or paste your
   own CSV into the **Workspace** view.
3. Click **Run Analysis**.
4. Click **Export Report** to download a Markdown report.

## CSV format

Required header columns (case-insensitive):

```
email,role,status,last_login,department,mfa,scim_external_id,manager,monthly_license_cost
```

- `role` — `Admin`, `Editor`, or `Viewer` (free text is accepted; only
  `Admin` affects scoring specifically)
- `status` — `active` or `inactive`
- `last_login` — `YYYY-MM-DD`
- `mfa` — `yes` or `no`
- `scim_external_id`, `department`, `manager` — leave blank if unknown; a
  blank value is treated as missing and will be flagged
- `monthly_license_cost` — a plain number (no currency symbol)

A sample file is included at `sample-data.csv`.

## Scoring logic (editable)

The scoring model lives in `index.html` inside the `<script>` block, in the
`analyze()` function and the `FLAG_WEIGHTS` / `ROLE_BASE` constants near the
top of the script. It's deliberately simple and documented in-app under
**Workspace → Scoring Settings** so a buyer can retune it without needing to
read the whole file:

- Role base: Admin 20, Editor 8, Viewer 4
- Staleness bonus: up to 30 points, scaled from days since last login
- Admin without MFA: +25
- Stale admin / stale user flag: +15 / +10
- Missing SCIM external ID: +10
- Missing department or manager: +8 each
- Paid inactive seat: +15 (waste = that user's monthly license cost)
- Risk is capped at 100 per user
- Readiness = 100 − average risk across all analyzed rows

## Google Workspace sync (optional)

Instead of exporting and pasting a CSV, you can click **Connect Google
Workspace** to pull your directory live via Google's Admin SDK
(`admin.directory.user.readonly` scope).

- Uses **your own** Google OAuth Client ID — no client secret is ever
  needed (token-model OAuth, entirely in the browser).
- Clicking **Connect Google Workspace** opens a small popup asking for your
  Client ID, pre-filled if you've saved one before.
- One-time setup, per Google Cloud project:
  1. Open the [Google Cloud Console](https://console.cloud.google.com/apis/credentials)
  2. Create an OAuth 2.0 Client ID of type **Web application**
  3. Add the origin you're serving this app from to **Authorized JavaScript
     origins**
  4. Paste the Client ID into the popup opened by **Connect Google
     Workspace**
- Once connected, the browser talks directly to `accounts.google.com` and
  Google's API — no server of ours sits in the middle.
- Requires the connected Google account to have rights to read the
  Workspace directory (typically a Workspace admin) and requires the Admin
  SDK API to be enabled on that Google Cloud project.

## Microsoft Entra ID sync (optional)

Instead of exporting and pasting a CSV, you can click **Connect Microsoft
Entra ID** to pull your directory live via Microsoft Graph
(`User.Read.All`, `Directory.Read.All` scopes), using MSAL.js.

- Uses **your own** Entra app registration (Client ID + Tenant ID) — no
  client secret is ever needed (browser-only auth-code + PKCE flow via
  MSAL).
- Clicking **Connect Microsoft Entra ID** opens a small popup asking for
  your Client ID and Tenant ID, pre-filled if you've saved them before.
- One-time setup, per Entra app registration:
  1. Open the [Azure Portal](https://portal.azure.com) → **Microsoft Entra
     ID** → **App registrations** → **New registration**
  2. Add a **Single-page application (SPA)** redirect URI pointing at the
     origin you're serving this app from
  3. Grant the delegated Microsoft Graph permissions **User.Read.All** and
     **Directory.Read.All**, and consent as an admin
  4. Paste the Client ID and Tenant ID into the popup opened by **Connect
     Microsoft Entra ID**
- Once connected, the browser talks directly to `login.microsoftonline.com`
  and Microsoft Graph — no server of ours sits in the middle.
- Requires the signed-in account to have rights to read the directory
  (typically a Global Reader or similar role) and admin consent granted on
  the app registration.

## Project structure

```
index.html          the entire application (HTML + CSS + JS, no dependencies)
sample-data.csv      static 14-row demo dataset used by "Load Demo Data";
                      "Generate Mock Data" produces a larger randomized set
                      in-browser instead and isn't a file on disk
README.md            this file
PRICING.md           suggested pricing and positioning notes
TRANSFER_NOTES.md    notes for a buyer taking this over
```

## Status

A working, self-contained access-review tool you can open and try immediately — not
a live, hosted product. It runs entirely in the browser, has no backend, and comes
with generated/sample data rather than any real usage history. See
`TRANSFER_NOTES.md` for what a buyer would want to build on top of it next.

## License

Transfers fully to the buyer on sale, per the terms of sale agreed at
purchase. No license is granted to any other party.
