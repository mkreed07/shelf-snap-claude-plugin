# Shelf-Snap

For Whatnot sellers who keep their inventory in [Shelf-Snap](https://shelf-snap.com). Run your Shelf-Snap + Whatnot routine by asking Claude: send a Shelf-Snap batch to Whatnot (to a show, to your shop, or held for a show later), copy your custom Whatnot shipping profiles into Shelf-Snap, and import your weekly Whatnot sales and show stats into Shelf-Snap without downloading or uploading a CSV.

## What you need

- Shelf-Snap Pro
- A Whatnot seller account, with Whatnot's own connector added to Claude (address `https://mcp.whatnot.com`; Whatnot's setup steps are at developers.whatnot.com/getting-started/mcp)
- A paid Claude plan (Pro or Max)

## Use it

After installing, open the plugin's Connectors tab and connect Shelf-Snap. Then start a chat and say "check my setup": Claude checks both connectors and walks you through anything missing. Then try:

- "Send my Christmas batch to Whatnot."
- "Sync my Whatnot shipping profiles."
- "Import my Whatnot sales for last week."
- "Import stats for my last show."

Claude always shows a preview and waits for your yes before it creates, changes, or imports anything.

## What's inside

- Five skills: getting started, send a batch to Whatnot, sync shipping profiles, import weekly sales, import show stats.
- One connector: Shelf-Snap's own server. Signing in happens on app.shelf-snap.com.

## Data

The Shelf-Snap connector reads and updates your own Shelf-Snap inventory, batches, shipping profiles, sales, and show stats. The skills use Whatnot's connector, which you add yourself, to read and create your Whatnot listings and to read your shows, shipping profiles, and reports. Whatnot sales and show reports go from Whatnot's download link straight to Shelf-Snap; Claude never opens them or reads buyer information. The plugin runs no scripts and sends nothing anywhere else.

Shelf-Snap is not affiliated with Whatnot. Support: support@shelf-snap.com
