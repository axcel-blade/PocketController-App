# Support

Help for the PocketController phone app (Android and iOS).

## Getting Help

- **Bug reports & feature requests** — open an [issue](https://github.com/axcel-blade/PocketController-App/issues).
- **Questions** — open a [discussion](https://github.com/axcel-blade/PocketController-App/discussions).
- **Security issues** — see [SECURITY.md](SECURITY.md).

## Before Opening an Issue

1. Check the [README](README.md) for how to run the app, and [app/README.md](app/README.md) for the Flutter project.
2. Read the error on the **PC connection** screen. Common causes:
   - The phone and PC are on different networks, or the Wi-Fi isolates clients.
   - The PC does not appear under **On your network**. The app probes UDP `5556` and listens on UDP `5557`. Entering the IP manually still works.
   - The connection screen says every controller slot is already in use.
3. Search [existing issues](https://github.com/axcel-blade/PocketController-App/issues).

## Providing Good Bug Reports

Please include:

- Phone model and Android or iOS version
- PocketController app version
- The error shown on the connection screen, if any
- Steps to reproduce
- Expected vs actual behaviour
