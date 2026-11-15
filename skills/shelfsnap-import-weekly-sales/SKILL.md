---
name: shelfsnap-import-weekly-sales
description: Import a seller's Whatnot sales for a week into Shelf-Snap without downloading or uploading a CSV, using the Shelf-Snap and Whatnot connectors. Use when a seller says "import my Whatnot sales for last week", "bring in my sales", "update my Shelf-Snap sales", or names a week (e.g. "sales for the week of Nov 2").
---

# Import weekly Whatnot sales into Shelf-Snap

You need BOTH connectors switched on in this chat: **Shelf-Snap** and **Whatnot**. If either is missing, tell the seller to click **+ → Connectors** and switch it on, then stop.

## Approval (every write)

Only an explicit yes to the exact preview you just showed counts as approval. A correction, a changed choice or a question is NOT a yes: update the plan, preview again (dry run), show it, and ask again. An earlier yes never carries over to a changed plan.

## Steps

1. **Check the plan.** Call Shelf-Snap `get_account`. If the plan isn't Pro (or Pro+), tell the seller the Shelf-Snap connector needs Shelf-Snap Pro, and stop.
2. **Work out the week.** Sales go one week at a time, the same weeks as Whatnot's weekly orders files: Monday 00:00 UTC to the next Monday 00:00 UTC. "Last week" is the latest such week that has ended. For several weeks: one report and one import per week. If it's unclear which week the seller means, ask.
3. **Ask Whatnot for the report.** With the Whatnot connector, call `generate_a_report` with `report_type` = `seller_financial_report`, `start_date` = that Monday at `00:00:00.000Z` and `end_date` = the next Monday at `00:00:00.000Z`.
4. **Wait for it.** Poll `get_a_report` about every 2 seconds until `status` is completed. Stop after a minute and tell the seller; if it failed or was cancelled, tell the seller.
5. **Never open, read or summarize the report file yourself: it holds buyer data.** Pass its `report_url` to Shelf-Snap's `import_whatnot_report` unchanged, with the same `report_type`.
6. **ALWAYS preview first:** `import_whatnot_report` with `dry_run = true` (nothing is saved; the result carries a `preview_token`). Show the seller everything the preview returns, the same as Shelf-Snap's import screen:
   - the week in Pacific time (`week.covers`);
   - how many rows matched automatically, the likely and uncertain matches (Whatnot listing title → Shelf-Snap item), and the unmatched rows (imported as unmatched sales, not linked to an item, unless the seller matches one to an item);
   - every "Same sale?" entry;
   - the import counts: sales recorded; tips, shipping, refunds; items marked sold; manual entries replaced; already imported (skipped); sold more than was on hand; refunds without a matched sale; rows skipped and why.
7. **Ask every "Same sale?" question exactly as given, with its choices exactly as given** (each hand-logged sale, then the new-sale choice). Never answer one for the seller. Pass the answers in `decisions.same_sale` (`{"<row>": "<answer from choices>"}`). A changed match goes in `decisions.matches` (`{"<row>": "<item_id>"}`, or `null` to import that row as unmatched). The import is refused while a "Same sale?" question is unanswered (`questions_open`). Any new or changed decision means: preview again with the new decisions, show it, and ask again.
8. **Only after the seller's explicit yes to that exact preview**, call `import_whatnot_report` with the same `report_url`, `report_type` and decisions, `dry_run = false` and that `preview_token`, and report what it did.

Importing the same week again is safe: sales already imported are skipped.

## Preview tokens

A real import (`dry_run = false`) needs the `preview_token` from the dry run the seller said yes to, for exactly the same request. `preview_required` = no matching preview (preview first). `preview_expired` = older than 30 minutes (preview again). `preview_changed` = Shelf-Snap changed since the preview, nothing was saved, and the result holds the new preview and token: show it and ask again.

## Never

- Read, repeat, pass to any tool, or ask for buyer names or any other buyer data.
- Answer a "Same sale?" question for the seller.

## How to talk to the seller

Plain words, short. Use Pacific time for dates. Don't show IDs or tool names.
