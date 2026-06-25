# Deep Space Royale — Combat Console

A mobile-friendly companion web app for **Deep Space Royale (DSR)**, a live ship-combat overlay game running on TerranEctomy's Twitch stream. Viewers use this page on their phone or second screen to pilot their ship and fire abilities in real time, instead of typing chat commands.

🔗 Live: https://shoddyinterceptor-beep.github.io/dsr-companion/

## What it does

- Connects directly to the streamer's Streamer.bot WebSocket server (URL is shared by the streamer each session — paste it into the connection box).
- Lets a viewer log in with Twitch (OAuth) to verify their identity before firing any commands.
- Sends the same commands as chat (`!fire`, `!torp`, `!heal`, `!cloak`, etc.) via Streamer.bot's `DoAction` API, with client-side cooldown tracking so buttons grey out when an ability is on cooldown.
- Mirrors live game state (HP, kill streaks, active powerups, lock-ons, Borg/Sphere boss fights, Teams mode) broadcast from the overlay, so the console always reflects what's actually happening on stream.
- Includes a vessel picker and the Power Store (powerup purchase) modal.

## How a viewer uses it

1. Open the page, log in with Twitch.
2. Paste the connection URL the streamer posts in chat/Discord that session (it's a Cloudflare tunnel URL, changes each stream).
3. Pick a vessel, then use the ability buttons to play.

No installation — it's a static page, just open it in a mobile browser.

## For maintainers (TerranEctomy)

- **Single file**: this repo is just `index.html` on `main` — GitHub Pages serves it directly, no build step.
- **Twitch OAuth**: `TWITCH_CLIENT_ID` and `TWITCH_REDIRECT` are hardcoded near the bottom of the `<script>` block. If the Twitch app registration or the Pages URL ever changes, update both constants there. The redirect URI must exactly match what's registered in the Twitch Developer Console.
- **Cooldown durations**: kept in the `COOLDOWNS_SECONDS` object near the top of the script. If a cooldown changes in Streamer.bot, update the matching value here too — they're tracked independently client-side.
- **Action names**: the `ACTION_NAMES` map ties each command type to the exact Streamer.bot action name. If an SB action gets renamed, update it here.
- **Game state**: arrives via WebSocket broadcast from the `DSR Store State` Streamer.bot action (same broadcast the overlay itself consumes), so this console and the overlay never drift out of sync on cooldowns, HP, or active powerups — only the button-level cooldown timers are predicted client-side between broadcasts.
- **Powerup blocks**: ability buttons grey out automatically based on `powerupBlockedAbilities` in the broadcast state — this file has no hardcoded per-powerup block list, so changes to which powerup blocks what live entirely in the overlay (`stream_avatars.html`), not here.

## Stack

Single static HTML file. No build tools, no dependencies beyond Google Fonts and the browser's native WebSocket/fetch APIs.
