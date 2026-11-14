---
name: shelfsnap-import-weekly-sales
description: Import a seller's Whatnot sales for a week into Shelf-Snap without downloading or uploading a CSV, using the Shelf-Snap and Whatnot connectors. Use when a seller says "import my Whatnot sales for last week", "bring in my sales", "update my Shelf-Snap sales", or names a week (e.g. "sales for the week of Nov 2").
---

# Import weekly Whatnot sales into Shelf-Snap

You need BOTH connectors switched on in this chat: **Shelf-Snap** and **Whatnot**. If either is missing, tell the seller to click **+ → Connectors** and switch it on, then stop.

## Steps

1. **Read the rules first.** Call Shelf-Snap `get_account`. Its import rules (steps i to vii) are the source of truth. Follow them exactly. If this skill and `get_account` ever disagree, `get_account` wins.
   - If the plan isn't Pro, tell the seller the Shelf-Snap connector needs Shelf-Snap Pro, and stop.
2. **Work out the week.** Sales import one week at a time: Monday 00:00 UTC to the next Monday 00:00 UTC, the same weeks as Whatnot's weekly orders files. "Last week" means the latest week that has ended. If the seller wants several weeks, do one week at a time (one report and one import each). If it's unclear which week they mean, ask.
3. **Ask Whatnot for the report.** Use the Whatnot connector's `generate_a_report` with `seller_financial_report` and that week's dates. Check `get_a_report` about every 2 seconds until it's done. After a minute, or if it failed, tell the seller.
4. **Never open, read, or summarize the report file.** It holds buyer information. Pass its `report_url` to Shelf-Snap's `import_whatnot_report` unchanged.
5. **Preview first** (`dry_run = true`). Show the seller everything it returns, the same as Shelf-Snap's import screen:
   - the week in Pacific time;
   - how many sales matched automatically, the likely and uncertain matches (Whatnot title → Shelf-Snap item), and the unmatched sales (saved as sales with no item unless the seller picks one);
   - every "Same sale?" question;
   - the counts the import will record (sales, tips, shipping, refunds, items marked sold, and anything skipped and why).
6. **Ask every "Same sale?" question exactly as given, with its choices exactly as given.** Never answer one for the seller. If the seller changes a match or answers a question, preview again and show the new result.
7. **Wait for a clear yes to that exact preview**, then import (same report and decisions, `dry_run = false`, the preview's token). Report what it did.

Importing the same week again is safe: sales already imported are skipped.

## Never

- Read, repeat, or ask for buyer names or any other buyer information.
- Answer a "Same sale?" question for the seller.

## How to talk to the seller

Plain words, short. Use Pacific time for dates. Don't show IDs or tool names.
