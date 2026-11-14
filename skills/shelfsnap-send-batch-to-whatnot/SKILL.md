---
name: shelfsnap-send-batch-to-whatnot
description: Send a Shelf-Snap batch (a show batch or a shop drop) to Whatnot as listings, using the Shelf-Snap and Whatnot connectors. Use when a seller says things like "send my Christmas batch to Whatnot", "list my batch for Friday's show", "put these Buy it Now items in my shop", or "re-send the item I deleted on Whatnot".
---

# Send a Shelf-Snap batch to Whatnot

You need BOTH connectors switched on in this chat: **Shelf-Snap** and **Whatnot**. If either is missing, tell the seller to click **+ → Connectors** and switch it on, then stop.

## Steps

1. **Read the rules first.** Call Shelf-Snap `get_account`. Its sending rules are the source of truth. Follow them exactly. If this skill and `get_account` ever disagree, `get_account` wins.
   - If the plan isn't Pro, tell the seller the Shelf-Snap connector needs Shelf-Snap Pro, and stop.
2. **Find the batch.** Call `list_batches` and match the name the seller used. If more than one batch could match, ask which one (show the names and dates).
3. **Read the items.** Call `get_batch_items` for that batch.
4. **Decide where the items go** (rule 1 in `get_account`):
   - the show the batch is linked to, if it has one;
   - otherwise the show or "the shop" the seller named (use the Whatnot connector's `list_shows` to find a named show, and confirm its name and date);
   - otherwise **held for a show later** (the default).
   - Buy it Now items go on sale in the shop ONLY if the seller clearly asked for that.
5. **Show the preview, always, even for one item:**
   - how many items and where they're going, in plain words: the show's name and date, "the shop (buyable now)", or "held for a show later";
   - a short table: title, price, sale type, quantity, category, shipping profile;
   - the items you'll skip and what each is missing, in plain words. Offer to help fill the gaps.
6. **Wait for a clear yes to that exact preview.** If the seller changes anything or asks a question, update the plan, show a new preview, and ask again.
7. **Create the listings** exactly as `get_account` steps 3 and 4 say (product, price and SKU, quantity, then merchandising, then `record_whatnot_listing`). Go about 2 Whatnot calls per second.
8. **Report back:** how many were created, where they are, and any that failed (title + the reason in plain words). For items held for a show later, tell the seller they're waiting in their Whatnot inventory, and they can say "add my [batch] to my [date] show" once the show exists.

## Re-sending an item the seller deleted on Whatnot

Follow `get_account` rule 5b: find the item, check with the Whatnot connector that the listing is really gone, tell the seller which item you'll clear, wait for their yes, then clear it and send it again (with the normal preview and yes). If the listing still exists on Whatnot, don't clear it. Tell the seller.

## Never

- Put a Buy it Now item on sale in the shop unless the seller asked.
- Set up giveaways (sellers do those by hand on Whatnot).
- Cancel orders, buy shipping labels, add tracking, or read buyer information.

## How to talk to the seller

Sellers are Whatnot resellers, not tech people. Use plain words and keep it short. Don't name tools or show IDs unless the seller needs them.
