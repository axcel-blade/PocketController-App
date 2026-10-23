# Contributing to PocketController-App

Thank you for your interest in contributing to the mobile app.

## Getting Started

1. Fork the repository and create your branch from `develop`.
2. Follow the [Git Flow](#git-flow) branching model.
3. Make sure the app analyzes and tests cleanly before opening a pull request.

## Git Flow

This is the required workflow. Only `main` exists in this repository today.

| Branch | Purpose |
|--------|---------|
| `main` | Stable releases only |
| `develop` | Integration branch for ongoing work |
| `feature/*` | New features branched from `develop` |
| `release/*` | Release preparation branched from `develop` |
| `hotfix/*` | Critical fixes branched from `main` |

## Prerequisites

- Flutter 3.x / Dart 3.x
- Android SDK for Android builds
- Xcode on macOS for iOS builds

## Building

```
cd app
flutter pub get
flutter analyze
flutter test
flutter run
```

## Protocol Changes

The packets the app sends and the replies it handles are documented in [docs/PROTOCOL.md](docs/PROTOCOL.md).
If you change them, update `app/lib/network/protocol.dart`, the app tests, and that document.

## Pull Request Guidelines

- Keep PRs focused — one feature or fix per PR.
- Update `CHANGELOG.md` under `[Unreleased]` with a summary of your change.
- When releasing the app, update:
  - `CHANGELOG.md` — move `[Unreleased]` entries into a dated `## [X.Y.Z] - YYYY-MM-DD` section
  - `README.md` — the version badge and the **Version** section
  - `app/pubspec.yaml` — `version: X.Y.Z+N` (always increase the build number `N`, even for a patch)
  - Tag the release `app-vX.Y.Z`
- Do not include build outputs or IDE files in your commit.

## Code Style

- Dart: follow the rules in `app/analysis_options.yaml` (`flutter analyze` must report no issues).
- No commented-out code or `TODO` left in production files — open an issue instead.
