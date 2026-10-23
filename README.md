# PocketController-App

<p align="center">
  <img src="docs/assets/logos/android-logo.png" alt="PocketController-App logo" width="128">
</p>

<!-- Dynamic badges (live data from GitHub) -->
[![CI](https://img.shields.io/github/actions/workflow/status/axcel-blade/PocketController-App/ci.yml?branch=main&label=CI&logo=flutter)](https://github.com/axcel-blade/PocketController-App/actions/workflows/ci.yml)
[![Latest release](https://img.shields.io/github/v/release/axcel-blade/PocketController-App?include_prereleases&sort=semver)](https://github.com/axcel-blade/PocketController-App/releases)
[![Open issues](https://img.shields.io/github/issues/axcel-blade/PocketController-App)](https://github.com/axcel-blade/PocketController-App/issues)
[![Last commit](https://img.shields.io/github/last-commit/axcel-blade/PocketController-App/main)](https://github.com/axcel-blade/PocketController-App/commits/main)
[![Stars](https://img.shields.io/github/stars/axcel-blade/PocketController-App?style=flat)](https://github.com/axcel-blade/PocketController-App/stargazers)

<!-- Static badges -->
[![License: MIT](https://img.shields.io/badge/license-MIT-c4f82a)](LICENSE.md)
[![Version](https://img.shields.io/badge/version-1.2.2-c4f82a)](CHANGELOG.md)
[![Flutter](https://img.shields.io/badge/Flutter-3.x-02569B?logo=flutter&logoColor=white)](https://flutter.dev/)
[![Platforms](https://img.shields.io/badge/platform-Android%20%7C%20iOS-5cd6e6)](app/README.md)
[![Protocol](https://img.shields.io/badge/protocol-UDP%20%C2%B7%2048%20bytes-5cd6e6)](docs/PROTOCOL.md)

This repository is the Flutter phone app for Android and iOS. It connects to [PocketController Server](https://github.com/axcel-blade/PocketController-Server).

## What this project does

```
app/ (Flutter)
├── screens/   (controller, connection)
├── widgets/   (controls, layout surface and picker)
├── models/    (gamepad state, layouts)
├── services/  (persisted settings)
└── network/   (connection, discovery, auto-connect)
```

- **Controls:** 8-way D-pad, A/B/X/Y, two analog sticks, LB/RB, analog LT/RT, Back / Home / Start — all multi-touch.
- **Layouts:** Classic, Racing and Compact, plus custom layouts you can drag, resize, save and delete.
- **Discovery and reconnect:** the app lists a PC on the same Wi‑Fi and rejoins it at launch, when it appears later, or after a dropped connection. Entering an IP manually still works.
- **Connection status:** *Connected* is shown only after an acknowledgement, with latency and specific errors otherwise.
- **Comfort:** haptic feedback (toggleable), screen-reader labels, 44 px minimum touch targets, sizing for small phones.
- Settings and layouts are stored on the phone.

| Setting   | Value |
|-----------|-------|
| UDP port  | 5555  |
| Discovery | UDP 5556 (probes) / 5557 (announcements) |
| Send rate | 60 Hz |

## Requirements

- Flutter 3.x / Dart 3.x
- An Android or iOS phone on the same Wi-Fi network as the PC

## Getting Started

1. Build and install this app (`cd app && flutter run`).
2. Open the **PC connection** screen (status chip, top left). Your PC appears under **On your network**; tap it. Or enter the IP address and port manually and tap **Connect**.

Next time, the app reconnects to the same PC automatically when it finds it.

Having trouble? See [SUPPORT.md](SUPPORT.md).

> The app is not compatible with DroidJoy or other controller servers. The packets it sends are documented in [docs/PROTOCOL.md](docs/PROTOCOL.md).

## Development

```sh
cd app
flutter analyze
flutter test
```

See [CONTRIBUTING.md](CONTRIBUTING.md). The Flutter project notes are in [app/README.md](app/README.md).

## Version

| Project | Version |
|---------|---------|
| PocketController-App app | **1.2.2** |

See the [changelog](CHANGELOG.md).

## Further reading

- [CONTRIBUTING.md](CONTRIBUTING.md)
- [SUPPORT.md](SUPPORT.md)
- [SECURITY.md](SECURITY.md)
- [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)
- [CHANGELOG.md](CHANGELOG.md)
- [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)
- [docs/PROTOCOL.md](docs/PROTOCOL.md)
- [LICENSE.md](LICENSE.md)
