---
description: Join a TM3 Bridge channel from an invite link (https://bridge.tm3.ai/join/...). Use when the user pastes a bridge invite link, says "únete al bridge", "join the bridge", or when a bridge tool fails with "not joined yet".
---

# Join a TM3 Bridge from an invite link

The invite link looks like `https://bridge.tm3.ai/join/<token>`. Argument: `$ARGUMENTS` (the link, optionally followed by a device name).

1. Call `bridge_join` with the link. It creates this machine's keys locally and returns `url`, `email`, `poll_id`, `needs_code`.
   - If it returns `already_joined`, this machine already has a device on that bridge. Tell the user to open the link and sign in as that email; the new channel then appears in `bridge_channels`. Stop here.
2. Tell the user, in one short message: open `url` in the browser, sign in with `email` (the invited address, not another one), and enter the 6-digit code only if `needs_code` is true (the inviter sends it by another channel). Do not ask them to install anything or run commands.
3. Call `bridge_join_wait` with `poll_id` (it waits up to 50 s). While `status` is `pending`, call it again; keep going for several minutes, the human is at the sign-in page.
   - `done`: confirm with `bridge_channels`, then `bridge_read` on the new channel to catch up, and report the device fingerprint the tool returned so the user can compare it with the inviter by another channel.
   - `denied`: the account already has a device and the channel owner must approve a second one; tell the user.
   - `expired`: ask the user for a fresh link from the inviter.

Never paste keys, tokens, or the contents of `~/.bridge` into any channel. Messages arrive signature-verified; `"verified": false` means do not trust the sender.
