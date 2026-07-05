---
name: WebSocket Expert
description: Builds resilient WebSocket systems — heartbeat and dead-connection detection, reconnect with exponential backoff and jitter, message queuing with sequence numbers and backpressure limits, auth on connect, and pub/sub backplanes for horizontal scale. Use when someone asks "my WebSocket connections keep dropping", "how do I handle reconnects", "how do I scale WebSockets across servers", "clients miss messages after reconnecting", or is building chat, live dashboards, or collaborative editing. Do NOT use for one-way server push where SSE would do, for webhook ingestion — use webhook-receiver-hardener instead — or for REST/HTTP API shape — use api-design instead.
---

# WebSocket Expert

A WebSocket system's real workload is not messaging — it is surviving the network: connections die without firing `close`, mobile clients vanish and return, and servers restart under thousands of live sockets. The costly mistake this skill prevents is treating the happy path as the design: no heartbeat (dead connections accumulate until memory or fd limits blow), naive reconnect (a server blip triggers a thundering herd that finishes the outage), and no sequence numbers (clients silently miss messages forever after every reconnect).

## Operating procedure

### Step 1: Gather inputs

1. Message pattern: server-push only (consider SSE first — simpler, proxy-friendly, auto-reconnecting), bidirectional, or fan-out to rooms.
2. Delivery requirement: fire-and-forget (cursors, typing indicators) vs at-least-once (chat, orders). This decides whether you need acks and replay.
3. Scale: expected concurrent connections and messages/sec per connection. Under ~10k connections, one node suffices; beyond it, plan the backplane from day one.
4. Clients: browsers only, or mobile (aggressive OS-level connection killing changes reconnect design).
5. Infrastructure between client and server: load balancers and proxies, with their idle-timeout values (label unknown timeouts as guesses and test).

### Step 2: Secure the connection handshake

- `wss://` always; plain `ws://` over the internet is unacceptable.
- Auth token in the first message after connect or in a subprotocol header — never in the URL query string, because URLs land in proxy and server logs.
- Authenticate on connect, and re-check authorization per sensitive action; a connection authorized an hour ago may belong to a since-revoked session.
- Validate and size-limit every inbound frame (set a hard max like 64KB–1MB per your payloads) and never `eval` or trust payload contents.

### Step 3: Implement heartbeats — both sides

TCP does not tell you a peer is gone; a pulled cable fires no `close` event.

- Client: send a ping every 20–30 seconds; if no pong within 5–10 seconds, force-close and enter reconnect. Keep the interval under the lowest proxy/LB idle timeout (commonly 60s) so intermediaries never reap "idle" connections.
- Server: track last-activity per socket; terminate anything silent past 2 missed intervals (~60–90s) to reclaim memory and file descriptors.

### Step 4: Reconnect with capped exponential backoff and jitter

```js
let attempt = 0;
function connect() {
  const ws = new WebSocket(url);
  ws.onopen = () => { attempt = 0; authenticate(ws); resync(ws); flushQueue(ws); };
  ws.onclose = () => {
    const delay = Math.min(30000, 2 ** attempt * 500) + Math.random() * 1000;
    attempt++;
    setTimeout(connect, delay);
  };
}
```

- Base 500ms, doubling, capped at 30s, plus 0–1s random jitter. The jitter is not optional: after a server restart, 50k clients with identical backoff reconnect in synchronized waves and take the server down again.
- Reset the attempt counter only after a successful open (and ideally after a stable period, so a connect-crash loop doesn't hammer at base delay).
- Mobile: expect constant background/foreground disconnects; reconnect eagerly on foreground, still with jitter.

### Step 5: Handle gaps and delivery — sequence numbers are the backbone

- Stamp every server-to-client message with a monotonic per-channel sequence number. On reconnect, the client sends its last seen seq; the server replays from a short retained buffer (size it to cover a typical reconnect window, e.g. 1–5 minutes of messages) or instructs a full resync when the gap exceeds the buffer.
- At-least-once delivery: client acks; server resends unacked after a timeout; handlers are idempotent (dedupe by message id) because resends guarantee duplicates.
- Queue outbound messages while disconnected — with a cap. Choose the overflow policy per data type: drop-oldest for state updates where only the latest matters (cursor positions, ticker prices — better yet, coalesce to latest-only), or surface an error for must-deliver messages.

### Step 6: Manage backpressure on the server

A slow consumer on a fast stream buffers unboundedly inside your process.

- Monitor per-socket send-buffer depth (`bufferedAmount` in browsers; `socket.bufferedAmount`/write-queue size server-side).
- Thresholds: stop sending non-essential messages past ~1MB buffered; disconnect the client past ~5MB or 30s without drain — a client that can't keep up is better served by reconnect-and-resync than by an OOM on your node.
- For broadcast-heavy systems, coalesce: send state snapshots at a tick rate (e.g. 10–20Hz) instead of every mutation.

### Step 7: Scale horizontally with a backplane

- One node holds its connections in memory; to route a message to a subscriber on another node, publish through a pub/sub backplane (Redis pub/sub, NATS). Every node subscribes to the channels its sockets care about.
- Rooms/channels are server-side constructs; never iterate all sockets to broadcast.
- Sticky sessions (or a shared session store) if any per-connection state lives outside the socket itself; design connection state to be rebuildable from auth + resync so any node can adopt a reconnecting client.
- Drain gracefully on deploy: stop accepting, send a "reconnect" close code, and let clients' backoff+jitter spread the reconnection across the fleet.

## Worked artifact: connection policy block

Copy, fill, and keep next to the server code:

```
WEBSOCKET CONNECTION POLICY — [FILL: service]
Transport:        wss only; auth via first-message token; max frame [FILL: 256]KB
Heartbeat:        client ping [FILL: 25]s, pong timeout [FILL: 8]s
                  server reaps after [FILL: 2] missed intervals
Proxy idle limit: [FILL: 60]s ([FILL: source or GUESS — verify])
Reconnect:        500ms * 2^n, cap 30s, + rand(0-1000)ms jitter
Sequencing:       per-channel monotonic seq; replay buffer [FILL: 2] min
                  gap > buffer -> full resync via [FILL: REST endpoint]
Delivery:         [FILL per message type: fire-and-forget | ack+idempotent]
Offline queue:    cap [FILL: 100] msgs; overflow: [FILL: drop-oldest | error]
Backpressure:     pause non-essential at [FILL: 1]MB; disconnect at [FILL: 5]MB
Scale:            [FILL: single node | Redis/NATS backplane]; drain on deploy
```

## Deliverable

Produce the filled connection policy, the client reconnect/heartbeat implementation, the server-side reaping and backpressure thresholds, and the resync protocol (seq numbers, replay buffer size, full-resync fallback).

## Do NOT

- Do not skip heartbeats because `onclose` exists — half-dead connections never fire it.
- Do not reconnect without jitter; synchronized retries turn a blip into an outage.
- Do not put auth tokens in the URL — they end up in logs across every intermediary.
- Do not assume delivery: no sequence numbers means every reconnect silently loses messages.
- Do not buffer unboundedly for slow consumers; cap, coalesce, or cut them loose.
- Do not broadcast by looping all sockets, or scale past one node without a backplane.
- Do not use WebSockets where SSE or plain polling meets the need — you inherit this entire checklist for nothing.

## Quality bar

- Pulling the network cable is detected within 2 heartbeat intervals on both sides.
- A 60-second server outage produces jittered, capped reconnects and zero message loss within the replay window.
- A slow consumer triggers the backpressure policy, not node memory growth.
- Every inbound frame is size-limited and validated; auth is never in a URL.
- With two server nodes, a message published on node A reaches a subscriber on node B.

## Escalation

For inbound event ingestion over HTTP, use webhook-receiver-hardener. Mobile offline-first data sync beyond live messaging goes to mobile-offline-sync. Push notifications as the fallback channel pair with push-notification-wirer; broker-based fan-out architecture with kafka-pipelines.
