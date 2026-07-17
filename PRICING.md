# Pricing Notes

## Editions sold

Sold as four separate Payhip variants, split by which live directory connector is included:

- **Scimly — Full (Google Workspace + Microsoft Entra ID)** — $999 *(this package)*: Both live directory connectors included: Google Workspace and Microsoft Entra ID.
- **Scimly — Google Workspace edition** — $450: Google Workspace live directory connector included. No Microsoft Entra ID connector.
- **Scimly — Microsoft Entra ID edition** — $450: Microsoft Entra ID live directory connector included. No Google Workspace connector.
- **Scimly — Base (CSV only)** — $350: CSV import only — no live directory connectors. Paste, upload, or drag in a CSV export.

## Rationale for tiering

- Price the base edition close to comparable CSV-only access-review starters on the market — it competes on being cheaper and just as capable for that scope.
- Price the single-connector editions at a real premium over base, since a working OAuth directory sync (Google Workspace token-model auth, or Microsoft Entra MSAL auth-code+PKCE) is functional depth most comparable listings don't include at all.
- Price the full edition below the combined cost of both single-connector editions, as the expected bundling discount.

## Positioning notes

No live customers, revenue, or production usage back this listing — describe it plainly as a working starter kit with sample/generated demo data, not as a proven product. Avoid implying otherwise in the listing copy.
