# Changelog

Changelog for the PocketController phone app.

## [Unreleased]

No notes yet.

## [1.2.2] - 2026-10-03

### Fixed
- App icon no longer has transparent corners: the iOS app icon set is a full lime square (corners were rendering black), and Android 8+ uses an adaptive icon (lime background + controller foreground) instead of a circle on a white plate
- Launch screen uses the app background colour (`#121417`) on Android and iOS instead of white
- Android 12+ launch screen shows the logo without the white circle behind it, using a dedicated transparent launch icon

## [1.2.1] - 2026-10-03

### Changed
- New controller logo for the Android app and iOS app: launcher icons and the iOS app icon set
- Source logos (1024×1024) live in `docs/assets/logos/`

## [1.2.0] - 2026-10-03

### Added
- **Mobile app redesign (Android + iOS):** graphite/lime/cyan theme; Classic, Racing and Compact layouts; custom layout editor (drag, resize, save, select, delete); haptics toggle; screen-reader labels; minimum touch targets and small-screen sizing; settings and layouts saved on the device
- **Connection screen** with verified status, round-trip latency and actionable error messages; "Connected" is only shown after the handshake is acknowledged
- **LAN discovery:** the app lists PCs on the network and connects with one tap. Manual IP entry is unchanged
- **Auto-connect:** the app joins the last-used PC whenever it is found (at launch, when the PC appears after the app, and after the connection drops); pressing Disconnect pauses it; can be turned off
- `docs/PROTOCOL.md` (wire format and discovery) and `docs/ARCHITECTURE.md`
- Flutter unit and widget tests

### Changed
- Triggers are analog pads; the D-pad supports diagonals and sliding; all controls are multi-touch
- Renamed the `frontend/` folder to `app/` and the `CI - Frontend` workflow to `CI - App` (`ci-app.yml`)

### Removed
- Gyroscope streaming (`sensors_plus`); the gyro fields are sent as zero

### Fixed
- Release builds lacked the Android `INTERNET` permission
- Farewell packets could be dropped when the socket was closed or would block
- The app connects via the address on its own subnet when the same PC is heard from several addresses

## [1.1.0] - 2026-06-14

### Added
- `frontend/` Flutter Android app: Xbox-style controller UI with dual analog sticks, D-pad, ABXY face buttons, LB/RB bumpers, LT/RT trigger sliders, Back/Start/Guide buttons
- `frontend/lib/network/protocol.dart`: 48-byte little-endian UDP packet serialization
- `frontend/lib/network/udp_client.dart`: UDP client with 60 Hz input send loop and 2-second ping keep-alive
- `frontend/lib/screens/connect_screen.dart`: IP/port entry screen
- `frontend/lib/screens/controller_screen.dart`: Landscape-locked full-screen controller UI with gyroscope support via `sensors_plus`
