---
name: Push Notification Wirer
description: Wires native mobile push end to end on APNs and FCM — device-token lifecycle, permission priming and prompt timing, alert and silent payloads, the server send path, and tap routing in all app states. Use when someone says "add push to our app", "the notification never arrives", "how do I handle FCM token refresh", or is wiring the .p8 or service-account sender and handling notification taps. Do NOT use for writing the notification text and message strategy — use push-notification-copy instead; not for web/browser push (Web Push/VAPID), in-app banners, or platform notification dashboards; and hand navigation from a tapped payload into a specific screen to a deep-link router.
---
# Push Notification Wirer

Wire native iOS (APNs) and Android (FCM) push as a verified pipeline: every hop fails silently — a wrong key, stale token, backgrounded app, or missing entitlement all produce "nothing happened" with no error — so prove each hop instead of assuming it. The costly failure is shipping push that works on the developer's device for a week, then silently decays as tokens rotate and permissions are denied.

## Operating procedure

### Step 1: Gather inputs

1. Platforms in scope (iOS, Android, both) and minimum OS versions — Android 13+ adds the POST_NOTIFICATIONS runtime permission.
2. Notification types planned: user-visible alerts, silent/background data pushes, or both. Silent push has hard throttling constraints; flag any feature that depends on guaranteed silent delivery as a design risk now.
3. Server stack and where tokens will be stored (must key by user AND device).
4. The value moment that will justify the permission prompt (order placed, message sent, price alert created). If nobody can name one, resolve that before wiring the prompt.
5. Who owns the message content — route copy, tone, and send-time strategy to push-notification-copy.

### Step 2: Register and persist the token

Register for remote notifications, capture the device token (APNs) or registration token (FCM), and POST it to the server keyed by user AND device. Send it on every launch and on the rotation callback (didRegisterForRemoteNotifications / FCM onNewToken) — never only once at signup. Tokens change on reinstall, restore, and refresh.

### Step 3: Run the token lifecycle, not just registration

Apply these lifecycle rules server-side:

| Event | Action |
|---|---|
| App launch | Upsert token with a fresh last-seen timestamp |
| Rotation callback fires | Upsert immediately; the old token is dead |
| User logs out | Delete the token server-side — never push account data to a logged-out device |
| Send returns APNs 410 or FCM UNREGISTERED | Delete the stored token on the spot |
| Token last-seen older than 1 month | Mark stale; exclude from bulk/campaign sends |
| Token last-seen older than 270 days | Prune it — this is FCM's own staleness guidance |

Skipping this table is why delivery stats lie: sends to dead tokens count as "sent" and never arrive.

### Step 4: Prime the permission, then prompt — timing rules

- Never trigger the OS prompt on first launch with no context; cold prompts are where opt-in goes to die.
- Show a pre-permission explainer tied to the value moment from Step 1, with a real yes/no choice. Fire the OS prompt only on yes.
- On iOS the OS prompt is effectively single-shot — a denial routes the user to Settings forever. If the user declines the explainer, do not burn the prompt; re-offer after two or more further value moments. iOS provisional authorization (quiet delivery, no prompt) is a legitimate middle path for content apps.
- On Android 13+ request POST_NOTIFICATIONS explicitly; after two denials the system stops showing the dialog, so the same primer-first rule applies.
- Handle the denied state without breaking the feature: the app must work, and show an inline "enable in Settings" affordance where the feature wants push.

### Step 5: Build the payload for the intended behavior

Alert push: aps.alert (iOS) or notification (FCM), shown by the OS. Silent/background push: content-available 1 plus the remote-notification background mode and low priority on iOS, or data-only on FCM — throttled and not guaranteed. Put routing info in custom data keys, never in visible text. Stay under 4KB (both APNs and FCM enforce a 4KB payload limit).

Bad payload (routing data in visible text, no routing key):

```json
{"aps": {"alert": "Order 8123 shipped! Open orders/8123 to track"}}
```

Good payload (clean copy, typed routing key in custom data):

```json
{
  "aps": {"alert": {"title": "Your order shipped", "body": "Arriving Thursday"}},
  "route": "order_detail",
  "order_id": "8123"
}
```

### Step 6: Send over the modern server path

APNs token-based auth (.p8 key + key id + team id) over HTTP/2 — not legacy certificates. FCM v1 API with a service account. Set apns-priority / FCM priority: high for user-visible, low/normal for silent. Log the APNs/FCM response id and HTTP status on every send so a non-delivery is debuggable rather than mysterious.

### Step 7: Handle taps in all three app states

A tap arrives in foreground, background, or terminated. Terminated launches deliver the payload through launch options / getInitialMessage, not the normal listener — wire and test that path explicitly. Parse the routing key into a typed in-app destination; hand the actual navigation to a deep-link router.

### Step 8: Verify the pipeline hop by hop

Prove, on real devices: token reaches the server; send returns 200 with an id; alert renders in foreground, background, and terminated; tap routes correctly from a cold terminated launch; logout deletes the token; a send to a deleted-app device returns 410/UNREGISTERED and prunes the row.

## Deliverable

Produce a wired, verified push pipeline: token registration on launch and rotation with the server-side lifecycle table implemented (410/UNREGISTERED pruning, 1-month stale flag, 270-day purge), a primed permission flow with the value moment named, alert and silent payload templates with routing keys under 4KB, a .p8/service-account sender that logs response id and status per send, tap handling covering all three app states, and the hop-by-hop verification checklist with each hop checked off on a real device.

## Quality bar

- Token is persisted per user+device and re-sent on launch and on every rotation callback.
- 410 / UNREGISTERED responses delete the stored token; tokens stale past 1 month are excluded from bulk sends.
- Permission is primed at a named value moment before the OS prompt; denial degrades gracefully.
- Alert vs silent payload shape is chosen deliberately, carries a typed routing key, and stays under 4KB.
- Server uses .p8 token auth over HTTP/2 (APNs) and FCM v1 with a service account; every send logs a response id + status.
- Tap routing is verified from a cold terminated state, not just foreground.

## Do NOT

- Do NOT register the token once at signup and never again — rotation silently strips delivery.
- Do NOT request notification permission on first launch with no context, or burn the iOS prompt after a declined primer.
- Do NOT put routing data in the visible alert text, or exceed the 4KB payload limit.
- Do NOT rely on silent push for guaranteed or timely delivery — it is throttled; design the feature to survive its absence.
- Do NOT use legacy APNs certificates or the legacy FCM HTTP API.
- Do NOT treat the payload as a raw URL to open blindly; map a routing key to a typed destination and let the deep-link router navigate.
- Do NOT keep pushing to a logged-out device or a dead token — delete on logout and on 410/UNREGISTERED.
- Do NOT hand-roll a segmented fan-out sender at scale — reach for a provider (FCM topics, OneSignal, a provider SDK), but keep owning token hygiene and permission UX.
- Do NOT write the notification copy here — message text, tone, and cadence belong to push-notification-copy.
