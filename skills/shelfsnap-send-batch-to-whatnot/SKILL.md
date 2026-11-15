---
name: shelfsnap-send-batch-to-whatnot
description: Send a Shelf-Snap batch (a show batch or a shop drop) to Whatnot as listings, using the Shelf-Snap and Whatnot connectors. Use when a seller says things like "send my Christmas batch to Whatnot", "list my batch for Friday's show", "put these Buy it Now items in my shop", or "re-send the item I deleted on Whatnot".
---

# Send a Shelf-Snap batch to Whatnot

You need BOTH connectors switched on in this chat: **Shelf-Snap** and **Whatnot**. If either is missing, tell the seller to click **+ → Connectors** and switch it on, then stop.

## Approval (every write)

Only an explicit yes to the exact preview you just showed counts as approval. A correction, a changed choice or a question is NOT a yes: update the plan, preview again, show it, and ask again. An earlier yes never carries over to a changed plan.

## Steps

1. **Check the plan.** Call Shelf-Snap `get_account`. If the plan isn't Pro (or Pro+), tell the seller the Shelf-Snap connector needs Shelf-Snap Pro, and stop.
2. **Find the batch.** Call `list_batches` and match the name the seller used. If more than one batch could match, ask which one (show the names and dates).
3. **Read the items.** Call `get_batch_items` for that batch (read every page).
4. **Decide WHERE the items go before creating anything:**
   - the batch's `whatnot_show_id` when it is set (that show);
   - otherwise the show or "the shop" the seller names (find a named show with the Whatnot connector's `list_shows` and confirm its name and date);
   - otherwise **held for a show later** (the default): auctions get no show; Buy it Now items get `reserve_for_live = true` and no show. Held items wait in the seller's Whatnot inventory and can't be bought yet.
   - Never put a Buy it Now item on sale in the shop unless the seller explicitly says so.
5. **ALWAYS show a preview and wait for an explicit yes, whatever the number of items.** The preview:
   - how many items you will create and the target, in these words: the show's name and date, "the shop (buyable now)", or "held for a show later";
   - for each item, a compact list or table: title, price, sale type, quantity, category and shipping profile;
   - the items you will skip and why (their `missing_for_whatnot`, in plain words). Skip those unless the seller fills the gap in the Shelf-Snap app.
   Create nothing on Whatnot until the seller says yes to that exact preview.
6. **For each item**, with the Whatnot connector:
   1. create the product with `external_id` = the Shelf-Snap `item_id`, title, description, the Whatnot category id, attributes, photos (in the order given) and the dimension profile;
   2. set price and SKU on its variant;
   3. set inventory (quantity) with an idempotency key;
   4. ALWAYS finish with the merchandising update: sale type, price, and the show / shop / no show from step 4, with `reserve_for_live` sent explicitly on Buy it Now items. Skipping merchandising leaves the listing showing "Missing price or transaction type".
7. **Only after merchandising succeeded**, call Shelf-Snap `record_whatnot_listing` with the Whatnot product id (and variant id). It marks the item exported in Shelf-Snap, the same as a CSV export, so a re-run won't list it twice.
8. Pace Whatnot calls to about 2 per second.
9. **Report back:** how many were created, where they are, and any that failed (title + the reason in plain words). For items held for a show later, tell the seller they're waiting in their Whatnot inventory, and they can say "add my [batch] to my [date] show" once the show exists.

## Re-sending an item the seller deleted on Whatnot

1. Find it with `get_batch_items` (`include_listed = true`; its `whatnot_listing` has the product id).
2. Check with the Whatnot connector that the product no longer exists. If it still exists, don't clear anything: tell the seller.
3. Tell the seller exactly which item (its title) and which Whatnot product id you will clear, and wait for their explicit yes.
4. Only then call `unrecord_whatnot_listing` with that product id as `expected_product_id`.
5. Send the item as usual (the preview and yes in step 5 apply).

## Never

- Put a Buy it Now item on sale in the shop unless the seller asked.
- Set up giveaways (sellers do those by hand on Whatnot).
- Cancel orders, buy shipping labels, add tracking, or read buyer or customer data.

## How to talk to the seller

Sellers are Whatnot resellers, not tech people. Plain words, short. Don't name tools or show IDs unless the seller needs them.
