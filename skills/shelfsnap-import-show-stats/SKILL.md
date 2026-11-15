---
name: shelfsnap-import-show-stats
description: Import a seller's Whatnot show stats (livestream results) into Shelf-Snap without downloading or uploading a CSV, using the Shelf-Snap and Whatnot connectors. Use when a seller says "import stats for my last show", "bring in this week's show stats", "update my show numbers in Shelf-Snap", or names a show or date.
---

# Import Whatnot show stats into Shelf-Snap

You need BOTH connectors switched on in this chat: **Shelf-Snap** and **Whatnot**. If either is missing, tell the seller to click **+ → Connectors** and switch it on, then stop.

## Approval (every write)

Only an explicit yes to the exact preview you just showed counts as approval. A correction, a changed choice or a question is NOT a yes: update the plan, preview again (dry run), show it, and ask again. An earlier yes never carries over to a changed plan.

## Steps

1. **Check the plan.** Call Shelf-Snap `get_account`. If the plan isn't Pro (or Pro+), tell the seller the Shelf-Snap connector needs Shelf-Snap Pro, and stop.
2. **Work out which shows.** "My last show" = the day of their most recent show; "this week" = this week's shows. If unsure, use the Whatnot connector's `list_shows` and confirm with the seller.
3. **Ask Whatnot for the report.** Call `generate_a_report` with `report_type` = `analytics_livestreams_report` and `all_matching.start_time` (start, end) covering the shows, or `specific_shows` with their show ids.
4. **Wait for it.** Poll `get_a_report` about every 2 seconds until `status` is completed. Stop after a minute and tell the seller; if it failed or was cancelled, tell the seller.
5. **Never open, read or summarize the report file yourself.** Pass its `report_url` to Shelf-Snap's `import_whatnot_report` unchanged, with the same `report_type`.
6. **ALWAYS preview first:** `import_whatnot_report` with `dry_run = true` (nothing is saved; the result carries a `preview_token`). For each show, tell the seller its plan:
   - `attach` (to which batch, and why);
   - `save_without_batch` (saved with no batch; list its candidate batches: the seller may pick one, leave it without a batch, or skip it);
   - `skipped`, `conflict` or `error`;
   - the stats it changes, and whether a batch gets its show date;
   - how many shows are to save and how many are already up to date.
7. **Let the seller choose batches.** Pass choices in `decisions.shows` (`{"<show_id>": "<batch_id>", "unmatched" or "skip"}`). Never choose for them. Any new or changed choice means: preview again, show it, and ask again.
8. **Only after the seller's explicit yes to that exact preview**, call `import_whatnot_report` with the same `report_url`, `report_type` and decisions, `dry_run = false` and that `preview_token`, and report what it did.

Importing the same shows again is safe: shows already up to date aren't written. Promotion stats aren't available from Whatnot this way. If the seller asks for them, say so.

## Preview tokens

A real import (`dry_run = false`) needs the `preview_token` from the dry run the seller said yes to, for exactly the same request. `preview_required` = no matching preview (preview first). `preview_expired` = older than 30 minutes (preview again). `preview_changed` = Shelf-Snap changed since the preview, nothing was saved, and the result holds the new preview and token: show it and ask again.

## Never

- Read, repeat, pass to any tool, or ask for buyer names or any other buyer data.

## How to talk to the seller

Plain words, short. Name shows by title and date (Pacific time). Don't show IDs or tool names.
