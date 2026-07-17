# Transfer Notes

Notes for whoever takes this project over after purchase.

## Current state (honest summary)

- Working, static, client-side-only dashboard. No server, no database, no
  accounts, no payment processing.
- Ships with a 14-row demo CSV (`sample-data.csv`) used by the "Load Demo
  Data" button — this is illustrative data only, not real user records.
- The CSV, analysis results, and generated report don't persist between
  browser sessions; closing the tab clears the workspace. There is no
  backend to lose that data from. The one exception: if you use **Connect Google Workspace** or **Connect Microsoft Entra ID**, the Client ID (and, for Entra, Tenant ID) is saved to this browser's `localStorage` so you don't have to re-enter it every time — that's the only thing this app stores locally.
- Google Workspace and Microsoft Entra ID live sync are both built (OAuth token flow for Google, MSAL auth-code+PKCE for Entra — neither needs a client secret).
- No live usage history: no signed-up customers, no traffic, no revenue.
  Any listing or pitch for this should describe it as a ready-to-run starter
  kit, not as a validated or in-use product.

## What's genuinely done

- CSV parsing (handles quoted fields and commas inside quotes)
- In-browser **mock data generator** (500 randomized, deliberately messy
  rows per click) for demoing at scale without real data — separate from
  the static 14-row `sample-data.csv` used by "Load Demo Data"
- Access-review flag logic: stale admin/user, admin without MFA, missing
  SCIM ID, missing department, missing manager, paid inactive seat
- Configurable stale-days threshold and review date
- 0–100 risk score per user and a 0–100% workspace readiness score
- Monthly/annualized seat-waste estimate
- Markdown report export and analyzed-table CSV export

## Known limitations / things worth knowing before you extend this

- Negative and non-numeric `monthly_license_cost` values are coerced
  to $0 rather than rejected; there's no input validation that warns on
  clearly invalid cost data (e.g. `-49` or `"forty-nine"`). Worth adding
  if this is extended for production use.
- An unquoted comma inside a numeric field (e.g. a cost value like
  `1,999` typed without quotes) will silently split into two CSV cells;
  only the first segment is read as the cost, the rest is dropped as an
  unlabeled extra column. This doesn't crash or corrupt other rows, but
  it will silently truncate a number if a buyer's export has this. Not
  fixed — flagging it here since it's the kind of thing worth mentioning
  proactively rather than letting a buyer discover it.

## What a buyer will likely want to add

Roughly in order of typical priority:

1. **Persistence** — a database (even something lightweight) so review
   runs and CSVs survive a page reload and can be shared across a team.
2. **Auth** — if more than one person on a team will use it, or if it's
   sold as a hosted product rather than handed off as source.
3. **Okta live sync** — the same OAuth-in-browser pattern used for Google and Entra could be extended to Okta for buyers whose org uses neither.
4. **Scheduled/recurring runs** — re-running the analysis on a cadence and
   diffing against the last run, with email or Slack alerting on new flags.
5. **Multi-workspace support** — if sold to an agency/consultant who
   reviews multiple client organizations.
6. **Export formats beyond Markdown** — CSV or PDF export of flagged rows
   for compliance audit trails.

## Where to make changes

Everything lives in `index.html`:

- CSS custom properties at the top of the `<style>` block control the
  entire visual theme (colors, fonts, spacing) — change these first for a
  quick rebrand.
- The scoring model (`FLAG_WEIGHTS`, `ROLE_BASE`, and the `analyze()`
  function) is isolated near the top of the `<script>` block and is safe
  to edit without touching rendering code.
- Rendering functions (`renderTable`, `renderReport`, `renderStats`,
  `renderChecklist`) are separate from the scoring logic, so UI changes
  don't require re-deriving the scoring math.

## Support

This package is sold as-is, transferable source code. There is no ongoing
support, hosting, or maintenance commitment from the original builder
beyond what's stated in the terms of sale.
