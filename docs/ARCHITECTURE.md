# PocketController Architecture

How the Flutter app in this repository is put together. Packet layout: [PROTOCOL.md](PROTOCOL.md).

The app draws the controller, turns touches into a gamepad state, and sends that state.

## Modules

| Path | Responsibility |
|------|----------------|
| `app/lib/main.dart` | Loads settings, creates the shared `GamepadState` and `BridgeConnection`, starts the app. |
| `app/lib/theme.dart` | Colour palette (graphite, lime accent, cyan detail) and Material theme. |
| `app/lib/models/gamepad_state.dart` | Live input state: button and D-pad bitmasks, stick axes (-1…1), triggers (0…1). Fires `onDiscreteChange` on press/release/recentre. |
| `app/lib/models/control_layout.dart` | `ControlKind`, `ControlPlacement` (normalized position + size) and `ControlLayout`; the Classic, Racing and Compact built-ins; JSON (de)serialization. |
| `app/lib/services/app_settings.dart` | Persists address/port, haptics, the selected layout and custom layouts with `shared_preferences`. |
| `app/lib/network/protocol.dart` | Serializes packets and parses replies. |
| `app/lib/network/discovery.dart` | LAN discovery: sends probes, listens for announcements, keeps the list of PCs found. |
| `app/lib/network/auto_connect.dart` | Runs discovery while disconnected and joins the known PC whenever it appears, with a 5 s retry delay; Disconnect pauses it (`pickAutoConnectTarget`). |
| `app/lib/network/bridge_connection.dart` | UDP socket, handshake, 60 Hz send loop, pings, latency, timeouts and error messages. Exposes `BridgeStatus`. |
| `app/lib/screens/controller_screen.dart` | Main screen: toolbar, control surface and layout editor. Releases all inputs when the app loses focus. |
| `app/lib/screens/connection_screen.dart` | Address/port entry, connect/disconnect, status, errors, haptics toggle, compatibility notes. |
| `app/lib/widgets/controls.dart` | Individual controls: `PadButton`, `FaceButtons`, `DpadControl`, `AnalogStick`, `TriggerPad`; haptics helper. |
| `app/lib/widgets/control_surface.dart` | Positions each control of a layout (`placementRect`), enforces minimum touch targets, handles drag/select in edit mode. |
| `app/lib/widgets/layout_sheet.dart` | Layout picker (select/delete) and save-layout dialog. |
| `app/lib/widgets/status_chip.dart` | Always-visible connection indicator. |

A layout is a list of placements. Each placement has a centre (`x`, `y` as fractions of the control area) and a `size` (a fraction of the area height). `placementRect` turns this into pixels, applies minimum sizes and keeps every control on screen, so one layout works on phones of different sizes. Every layout contains every control. Custom layouts are stored as JSON. If a saved layout is missing a control, it is filled in from Classic.

### Message flow

```
touch ─▶ control widget (raw pointer events) ─▶ GamepadState ─┬─▶ onDiscreteChange ─▶ BridgeConnection.sendNow()
                                                              └─▶ 60 Hz timer ──────▶ BridgeConnection Input packet
```

- Controls use `Listener` pointer events rather than gesture detectors, so several controls can be held at the same time.
- The protocol sends the whole state every time, so a lost packet is corrected by the next one (at most ~16 ms later). Press and release events are also sent right away.
- Important packets are retried for a short time if the socket is busy (`send` returns 0).

### Session lifecycle

```
disconnected ──connect()──▶ connecting ──ConnectAck──▶ connected
      ▲                         │                          │
      │                    timeout / ServerFull       no Pong for 4 s / NotConnected / socket error
      │                         ▼                          ▼
      └────disconnect()──── error ◀─────────────────────────┘
```

The app never assumes it is connected. It only sends input while `connected`. On disconnect it sends an all-released input and then `Disconnect`.

### Discovery

`AutoConnector` keeps discovery running whenever the app is not connected, stops it once connected, and joins the known PC whenever it appears (at most every 5 s). Pressing Disconnect pauses it. Manual IP entry stays available. See [PROTOCOL.md](PROTOCOL.md#lan-discovery).

## Testing

- `app/test/protocol_test.dart` — packet layout, reply parsing, and `BridgeConnection` against a fake UDP peer on loopback (handshake, press/release delivery, timeout, full slot).
- `app/test/discovery_test.dart` — discovery packet format, finding a fake peer on loopback, and the auto-connect selection rules.
- `app/test/layout_and_ui_test.dart` — layout completeness, small-screen fit, persistence, and widget tests for buttons, multi-touch sticks, the D-pad and triggers.
