---
name: shelfsnap-sync-shipping-profiles
description: Copy a seller's custom Whatnot shipping profiles into Shelf-Snap, then help them clean up Shelf-Snap shipping names Whatnot no longer has, using the Shelf-Snap and Whatnot connectors. Use when a seller says "sync my Whatnot shipping profiles", "my shipping profile isn't showing in Shelf-Snap", "I renamed a profile on Whatnot", or "clean up my shipping profiles".
---

# Sync Whatnot shipping profiles into Shelf-Snap

You need BOTH connectors switched on in this chat: **Shelf-Snap** and **Whatnot**. If either is missing, tell the seller to click **+ → Connectors** and switch it on, then stop.

## What sellers should know (tell them when it helps)

- Whatnot's standard profiles (like "1 lb" or "8-11 oz") already work in Shelf-Snap. This sync is for the seller's OWN custom profiles.
- The sync goes one way: Whatnot → Shelf-Snap. To make a new profile, the seller creates it on Whatnot first, then syncs again.
- Names must match exactly, spaces and capital letters included.
- When a seller renames or edits a profile on Whatnot, Whatnot treats it as a new profile and retires the old one. The cleanup below handles that.
- Sold items always keep the shipping name they sold with.

## Approval (every write)

Only an explicit yes to the exact preview you just showed counts as approval. A correction, a changed choice or a question is NOT a yes: update the plan, preview again (dry run), show it, and ask again. An earlier yes never carries over to a changed plan.

## Steps

1. **Check the plan.** Call Shelf-Snap `get_account`. If the plan isn't Pro (or Pro+), tell the seller the Shelf-Snap connector needs Shelf-Snap Pro, and stop.
2. **Read every profile from Whatnot.** Call the Whatnot connector's `list_dimension_profiles` and read EVERY page (it returns only the seller's own ACTIVE custom profiles). Keep that full list: steps 3, 5 and 7 use it.
3. **Preview the sync.** Call `save_whatnot_shipping_profiles` with `dry_run = true` and the profiles: each one's id, exact name (character for character, spaces included), weight value and unit, and active flag; up to 100 per call. Nothing is saved. The result carries a `preview_token`.
4. **Show the preview and get a yes:**
   - how many profiles will be added / updated / unchanged;
   - rejections with their reasons (`duplicate_id` = that Whatnot id is already saved under another name in Shelf-Snap: tell the seller, change nothing);
   - `near_misses`: Shelf-Snap names that differ from a Whatnot name only by capital letters or spaces. Point them out; don't change them.
   After the seller's explicit yes, call it again with the same profiles, `dry_run = false` and that `preview_token`, and report what it did. If `more_to_fill` is true, tell the seller, preview again with the same profiles and ask again before the next save.
5. **Find stale names.** Call `list_stale_shipping_profiles` with the same full Whatnot list. For EACH entry, show the seller:
   - the name and what's wrong: `retired` (Whatnot retired it, usually because it was renamed or edited there), `not_on_whatnot` (it isn't on their Whatnot), or `item_only` (only items use that name and it's no saved profile);
   - how many unsold items and templates use it, and how many sold or out items keep it (those never change);
   - its suggestions (same weight, or the same name apart from capitals and spaces).
   You may confirm a retired one with the Whatnot connector's `get_a_dimension_profile` (it shows `active: false`).
6. **Let the seller choose, for each entry:** Replace (with which active profile: a suggestion or another one they name), Add on Whatnot (they create it there, then you sync again), Delete (only when no unsold item or template uses it), or Keep. Never choose for the seller, and never delete a profile that's in use.
7. **For Replace or Delete:** call `resolve_shipping_profile` with `dry_run = true` (and the same full Whatnot list), show exactly what it will do (how many items and templates move to the new name, whether the Shelf-Snap profile is deleted), and wait for an explicit yes. Then call it again with `dry_run = false` and that `preview_token`, and report what it did. Each choice is its own preview and its own yes. Add on Whatnot and Keep need no call.

## Preview tokens

A real save or resolve (`dry_run = false`) needs the `preview_token` from the dry run the seller said yes to, for exactly the same request. `preview_required` = no matching preview (preview first). `preview_expired` = older than 30 minutes (preview again). `preview_changed` = Shelf-Snap changed since the preview, nothing was saved, and the result holds the new preview and token: show it and ask again.

## How to talk to the seller

Plain words, short. Say "shipping profile", not "dimension profile". Don't show IDs unless the seller asks.
