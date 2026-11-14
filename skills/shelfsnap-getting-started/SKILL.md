---
name: shelfsnap-getting-started
description: Check a seller's Shelf-Snap + Whatnot setup in Claude and walk them through anything missing, then show what they can ask for. Use when a seller says "set me up", "get started with Shelf-Snap", "is my Shelf-Snap connected?", "check my setup", or seems unsure what this plugin does.
---

# Get started with Shelf-Snap in Claude

Check the setup in this order, and stop at the first thing that's missing. Tell the seller how to fix it in plain words.

## 1. Is the Shelf-Snap connector connected?

If Shelf-Snap's tools (like `get_account`) aren't available in this chat, tell the seller:

1. Go to **Customize → Plugins → Shelf-Snap**, open the **Connectors** tab, and click **Connect** next to Shelf-Snap.
2. On the Shelf-Snap sign-in page, choose the account that has Shelf-Snap Pro, then click **Allow**.
3. Start a new chat and say "check my setup" again.

If it's connected, call `get_account`. If the plan isn't Pro, explain that the Shelf-Snap connector needs Shelf-Snap Pro (upgrade in the Shelf-Snap app, then connect again), and stop.

## 2. Is the Whatnot connector connected?

Whatnot's connector is Whatnot's own, so it isn't part of this plugin. If Whatnot's tools (like `get_the_current_user`) aren't available, walk the seller through adding it, one step at a time:

1. Go to **Customize → Connectors**, click **+ Add**, then **Add custom connector**.
2. Name it **Whatnot** and paste this address: `https://mcp.whatnot.com`. Click Continue.
3. Where it asks about the OAuth client, choose **Use your own OAuth client** and paste this client ID: `bb1bf1ca-0452-4737-ad8f-81175f1601b8`. Leave the client secret blank.
4. Click **Add**, then **Connect**, sign in to Whatnot, and approve.
5. Start a new chat and say "check my setup" again.

(These steps come from Whatnot's developer page: developers.whatnot.com/getting-started/mcp.) The free Claude plan allows only one custom connector, so if Claude won't let them add Whatnot, they need a paid Claude plan.

If it's connected, call `get_the_current_user` to confirm which Whatnot account it is, and tell the seller the shop name so they can check it's the right one.

## 3. Quick test

Call `list_batches` and show the seller their most recent few batches (name, type, item count). Then tell them they're all set.

## 4. What they can ask for

Tell the seller they can just say:

- "Send my [batch name] to Whatnot." (Claude always shows a preview first and waits for their yes.)
- "Sync my Whatnot shipping profiles."
- "Import my Whatnot sales for last week."
- "Import stats for my last show."

## If something we announced isn't working

Claude keeps an old list of what Shelf-Snap can do. The fix: disconnect and reconnect Shelf-Snap (Customize → Plugins → Shelf-Snap → Connectors), then start a new chat.

## How to talk to the seller

Sellers are Whatnot resellers, not tech people. Plain words, short, one step at a time. Don't name tools or show IDs. Help email: support@shelf-snap.com.
