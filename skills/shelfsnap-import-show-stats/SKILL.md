---
name: shelfsnap-import-show-stats
description: Import a seller's Whatnot show stats (livestream results) into Shelf-Snap without downloading or uploading a CSV, using the Shelf-Snap and Whatnot connectors. Use when a seller says "import stats for my last show", "bring in this week's show stats", "update my show numbers in Shelf-Snap", or names a show or date.
---

# Import Whatnot show stats into Shelf-Snap

You need BOTH connectors switched on in this chat: **Shelf-Snap** and **Whatnot**. If either is missing, tell the seller to click **+ → Connectors** and switch it on, then stop.

## Steps

1. **Read the rules first.** Call Shelf-Snap `get_account`. Its import rules (steps ii to vii) are the source of truth. Follow them exactly. If this skill and `get_account` ever disagree, `get_account` wins.
   - If the plan isn't Pro, tell the seller the Shelf-Snap connector needs Shelf-Snap Pro, and stop.
2. **Work out which shows.** "My last show" = the day of their most recent show; "this week" = this week's shows. If unsure, use the Whatnot connector's `list_shows` and confirm with the seller.
3. **Ask Whatnot for the report.** Use `generate_a_report` with `analytics_livestreams_report`, covering those shows (a time range, or the specific shows). Check `get_a_report` about every 2 seconds until it's done. After a minute, or if it failed, tell the seller.
4. **Never open, read, or summarize the report file.** Pass its `report_url` to Shelf-Snap's `import_whatnot_report` unchanged.
5. **Preview first** (`dry_run = true`). For each show, tell the seller in plain words:
   - which Shelf-Snap batch it will attach to and why, or that it will be saved with no batch (list the batches it could go with);
   - the stats it adds or changes, and whether a batch gets its show date;
   - anything skipped, in conflict, or failed;
   - how many shows will be saved and how many are already up to date.
6. **Let the seller choose batches.** For a show with no batch, they can pick one, leave it without a batch, or skip it. Never choose for them. Any change means a new preview.
7. **Wait for a clear yes to that exact preview**, then import (same report and choices, `dry_run = false`, the preview's token). Report what it did.

Importing the same shows again is safe: shows already up to date aren't changed. Promotion stats aren't available from Whatnot this way. If the seller asks for them, tell them so.

## Never

- Read, repeat, or ask for buyer names or any other buyer information.

## How to talk to the seller

Plain words, short. Name shows by title and date (Pacific time). Don't show IDs or tool names.
