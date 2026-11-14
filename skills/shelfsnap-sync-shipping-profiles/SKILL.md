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
- When a seller renames or edits a profile on Whatnot, Whatnot treats it as a new profile and retires the old one. The cleanup step (below) handles that.
- Sold items always keep the shipping name they sold with.

## Steps

1. **Read the rules first.** Call Shelf-Snap `get_account`. Its shipping-profile rules (steps a to f) are the source of truth. Follow them exactly. If this skill and `get_account` ever disagree, `get_account` wins.
   - If the plan isn't Pro, tell the seller the Shelf-Snap connector needs Shelf-Snap Pro, and stop.
2. **Read every profile from Whatnot.** Use the Whatnot connector's `list_dimension_profiles` and read EVERY page. Keep the full list; later steps need it.
3. **Preview the sync.** Call `save_whatnot_shipping_profiles` with `dry_run = true`. Show the seller, in plain words:
   - how many profiles will be added, updated, or left as they are;
   - anything rejected, and why;
   - near-misses: Shelf-Snap names that differ from a Whatnot name only by capitals or spaces. Point them out; don't change them.
4. **Wait for a clear yes**, then save (same profiles, `dry_run = false`, the preview's token). Report what it did. If it says there's more to fill, tell the seller and preview again before the next save.
5. **Cleanup.** Call `list_stale_shipping_profiles` with the same full Whatnot list. For EACH entry, tell the seller:
   - the name and what's wrong (Whatnot retired it, it isn't on their Whatnot, or only items use it);
   - how many unsold items and templates use it, and how many sold items keep it (those never change);
   - the suggested replacements.
6. **Let the seller choose for each one.** The choices are Replace (with which profile), Add it on Whatnot (they create it there, then you sync again), Delete (only if nothing unsold uses it), or Keep. Never choose for them.
7. **For Replace or Delete:** call `resolve_shipping_profile` with `dry_run = true`, show exactly what will change, wait for a clear yes, then apply it. Each choice gets its own preview and its own yes.

## How to talk to the seller

Plain words, short. Say "shipping profile", not "dimension profile". Don't show IDs unless the seller asks.
