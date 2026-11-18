# HA-RAPTBridge

Unofficial Home Assistant bridge between RAPT Brew devices and BrewAssistant.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
![Project status](https://img.shields.io/badge/status-pre--alpha-orange)

> [!WARNING]
> HA-RAPTBridge is currently a design and research project. It is not installable
> yet and must not be used to control heating or pumps.

## Why this project exists

RAPT is migrating existing devices to a new backend architecture. The legacy
RAPT portal is scheduled for deprecation on **2026-12-31**. HA-RAPTBridge is a
new, independent Home Assistant integration intended to connect the new RAPT
Brew backend to BrewAssistant without carrying forward assumptions from the
legacy API.

The project is developed from scratch. The existing RAPT Cloud Link integration
is used as a behavioural reference so that BrewAssistant can receive the same
logical state and command capabilities during migration.

## Goals

- expose stable Home Assistant entities for RAPT Brew devices;
- normalize vendor payloads behind a provider-independent data model;
- preserve the BrewAssistant-facing temperature, control and profile-runtime
  contract where the new backend supports it;
- allow the legacy and new integrations to run side by side during validation;
- fail closed: never invent state or issue compensating OFF/STOP commands after
  a connection loss;
- introduce physical control gradually: read-only first, then supervised
  commands, then wider automation only after live verification.

## Initial scope

The first replacement target is the BrewZilla path used by BrewAssistant:

- internal kettle/wort temperature;
- external/control-device temperature and BLE thermometer role;
- target temperature;
- heating and pump state/utilisation;
- connection and telemetry freshness;
- active profile/session, current step and next step;
- verified start/end profile commands.

RAPT Pill, temperature-controller and bonded-device coverage remain in scope,
but they are not allowed to delay a safe BrewZilla read-only milestone.

## Planned integration identity

- Home Assistant domain: `rapt_brew_bridge`
- Repository: `Jocke1970/HA-RAPTBridge`
- Release flow: `dev` → `beta` → `main`

A new domain is intentional. It allows parallel installation and comparison
with the legacy `rapt_cloud_link` integration before cutover.

## Documentation

- [Architecture](docs/ARCHITECTURE.md)
- [BrewAssistant compatibility contract](docs/COMPATIBILITY_CONTRACT.md)
- [Roadmap](docs/ROADMAP.md)
- [RAPT backend research notes](docs/RAPT_BACKEND_RESEARCH.md)
- [Credits and notices](NOTICE.md)

## Safety principles

- Cloud/API availability is not equivalent to device availability.
- Stale telemetry must be marked stale or unavailable, never silently reused.
- A successful HTTP response is not authoritative confirmation of physical
  device state.
- Commands must be confirmed by a subsequent observed state update.
- If the provider is unavailable, BrewAssistant stops issuing new commands.
  It must not send automatic OFF/STOP commands merely because communication
  disappeared.
- Physical control starts in BrewAssistant's supervised/confirmed mode.

## Development status

No production code has been committed yet. The current work is defining the
boundary between the RAPT Brew transport and the stable Home Assistant /
BrewAssistant contract before credentials, endpoints or payload assumptions are
embedded in code.

## Credits

HA-RAPTBridge is maintained by
[Joachim Eriksson / Jocke1970](https://github.com/Jocke1970).

The behavioural compatibility target is informed by
[`berra200/home-assistant-rapt-cloud-link`](https://github.com/berra200/home-assistant-rapt-cloud-link),
originally created by [berra200](https://github.com/berra200), and by the
BrewAssistant-specific work previously maintained in
[`Jocke1970/home-assistant-rapt-cloud-link`](https://github.com/Jocke1970/home-assistant-rapt-cloud-link).

See [NOTICE.md](NOTICE.md) for the complete attribution and trademark notice.

## License

HA-RAPTBridge is licensed under the [MIT License](LICENSE).
