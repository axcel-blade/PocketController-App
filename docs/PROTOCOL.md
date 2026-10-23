# PocketController Wire Protocol

Packets are sent to [PocketController Server](https://github.com/axcel-blade/PocketController-Server). This document describes what the phone app sends and what replies it handles.

The app does **not** use WebSockets and is **not** compatible with DroidJoy or any other controller server.

## Transport

| Item         | Value                                      |
|--------------|--------------------------------------------|
| Transport    | UDP, unicast                               |
| Default port | `5555`                                     |
| Packet size  | 48 bytes, every packet, both directions    |
| Byte order   | Little-endian                              |
| Input rate   | 60 Hz, plus an immediate packet on every press/release |
| Keep-alive   | Ping every 1 s                             |

## Packet layout

| Offset | Size | Type      | Field          | Notes |
|-------:|-----:|-----------|----------------|-------|
| 0      | 1    | `uint8`   | `type`         | See message types below |
| 1      | 2    | `uint16`  | `buttons`      | Bitmask, see below |
| 3      | 4    | `float32` | `leftStickX`   | -1.0 (left) … 1.0 (right) |
| 7      | 4    | `float32` | `leftStickY`   | -1.0 (down) … 1.0 (up) |
| 11     | 4    | `float32` | `rightStickX`  | -1.0 … 1.0 |
| 15     | 4    | `float32` | `rightStickY`  | -1.0 … 1.0 (up positive) |
| 19     | 4    | `float32` | `leftTrigger`  | 0.0 … 1.0 |
| 23     | 4    | `float32` | `rightTrigger` | 0.0 … 1.0 |
| 27     | 1    | `uint8`   | `dpad`         | Bitmask, see below |
| 28     | 12   | `float32`×3 | `gyroX/Y/Z`  | Reserved; the app sends 0 |
| 40     | 8    | `int64`   | `timestampMs`  | Sender clock, ms since Unix epoch |

### Buttons bitmask

| Bit | Button | Bit | Button |
|----:|--------|----:|--------|
| 0 | A  | 6  | Start |
| 1 | B  | 7  | Back |
| 2 | X  | 8  | Left stick click (not exposed in the app UI) |
| 3 | Y  | 9  | Right stick click (not exposed in the app UI) |
| 4 | LB | 10 | Home / Guide |
| 5 | RB |    |       |

### D-pad bitmask

`Up = 1`, `Down = 2`, `Left = 4`, `Right = 8`. Diagonals set two bits.

## Message types

The app sends:

| Value | Name         | Meaning |
|------:|--------------|---------|
| 0 | `Connect`    | Request a controller slot. The app resends every 600 ms for up to 3 s until answered. |
| 1 | `Disconnect` | Leave. Preceded by an all-released `Input`. |
| 2 | `Input`      | Full controller state. Sent at 60 Hz and immediately on press/release/recentre. |
| 3 | `Ping`       | Keep-alive; `timestampMs` is echoed back in the `Pong`. |

Replies the app handles (all fields zero except `type` and the echoed `timestampMs`):

| Value | Name           | When the app receives it |
|------:|----------------|--------------------------|
| 4 | `ConnectAck`   | The `Connect` was accepted. |
| 5 | `Pong`         | Reply to `Ping`. Round-trip latency = now − echoed timestamp. |
| 6 | `ServerFull`   | `Connect` was refused because every slot is in use. |
| 7 | `NotConnected` | `Ping` was answered for a phone that has no session. |

## LAN discovery

Discovery lets the app find a PC without typing an IP. It uses short UTF-8 text packets on
**separate ports**, so it never mixes with the 48-byte controller packets above.

| Port | What the app does |
|-----:|-------------------|
| 5556 | Sends a probe. |
| 5557 | Listens for announcements. |

| Packet | Format |
|--------|--------|
| Probe | `PCTRL?1\|<device name>` |
| Announcement | `PCTRL!1\|{"name":"GAMING-PC","port":5555,"clients":1,"max":4,"id":"3f9c0a1b2c4d","ips":["192.168.0.5","172.20.0.1"]}` |

* `name` is the name to show in the list, `port` is the controller port to connect to, and
  `clients`/`max` show how many slots are in use.
* The app groups announcements that share an `id` and connects through the address in `ips` that
  is on the phone's own subnet. Both fields are optional; without them the app uses the source
  address and port.
* The app sends probes every 2 s to `255.255.255.255`, to its subnet broadcast address, and
  (every other round) to each host in its /24 subnet. The unicast sweep matters where broadcasts
  are blocked, such as on iOS without the multicast entitlement or on some routers.
* A PC is dropped from the list after 7 s without an announcement or probe reply.
* Discovery only fills the list. Connecting still uses the `Connect` / `ConnectAck` handshake.

### Auto-connect

With **Connect automatically** on (the default), whenever the app is not connected it joins:

1. the last-used address and port, or
2. the last-used name, if its address changed, or
3. right after launch only, if nothing was ever saved: the only PC found within 8 s,
   if exactly one has a free slot.

This covers the PC appearing after the app is open, and reconnecting after Wi‑Fi drops. Attempts
are at least 5 s apart. Pressing **Disconnect** (or **Cancel**) pauses auto-connect until the user
connects again or relaunches the app. It never picks between several unknown PCs.

## Connection state

* **Connecting** — after `Connect` is sent, until a reply arrives.
* **Connected** — only after `ConnectAck`. Never assumed.
* **Error** — no reply within 3 s, `ServerFull`, `NotConnected`, no reply to pings for 4 s, or a socket error.

Input is only transmitted while connected. When the app is backgrounded, every input is released.
