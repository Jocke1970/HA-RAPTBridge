# BrewAssistant compatibility contract

## Status

This document defines the intended logical compatibility boundary. It is not
an assertion that the new RAPT Brew backend already exposes every required
field or command.

"Compatible" means BrewAssistant receives equivalent meaning and safety
behaviour. It does not require identical raw RAPT payloads or identical Home
Assistant entity IDs.

## Contract priorities

- **P0 — replacement blocker:** required before the legacy BrewZilla transport
  can be retired.
- **P1 — operational parity:** required before normal supervised brew-day use.
- **P2 — extended parity:** useful legacy coverage that may follow later.

## BrewZilla state contract

| Priority | Capability | Required behaviour |
|---|---|---|
| P0 | Provider availability | Distinguish API reachability, device presence and fresh telemetry. |
| P0 | Connection state | Expose connected/disconnected/unknown without inventing a default. |
| P0 | Internal temperature | Kettle/wort temperature with unit, timestamp and stable device identity. |
| P0 | Target temperature | Observed device target; never substitute the last command request. |
| P0 | Heating enabled | Observed boolean or unavailable. |
| P0 | Heating utilisation | Observed percentage with validated range. |
| P0 | Pump enabled | Observed boolean or unavailable. |
| P0 | Pump utilisation | Observed percentage with validated range. |
| P0 | Freshness | Expose provider/device timestamp and calculated observation age. |
| P1 | Control-device temperature | External temperature with validity and source diagnostics. |
| P1 | Logical BLE thermometer role | Mark a valid external process temperature for BrewAssistant source arbitration. |
| P1 | Active profile | Confirm active/inactive from a fresh runtime snapshot. |
| P1 | Current profile step | ID, order/name, target and relevant control settings. |
| P1 | Next profile step | ID/name/target when provided. |
| P1 | Profile session | Stable profile/session identity and start information. |
| P2 | Full normalized step list | Bounded list suitable for Home Assistant attributes. |
| P2 | PID details | Observed state and settings where the provider exposes them safely. |
| P2 | Raw-payload diagnostics | Redacted, bounded discovery metadata for development only. |

## Temperature roles

The new integration must preserve these semantics:

| Role | Meaning |
|---|---|
| `wort_or_kettle_temperature` | BrewZilla internal process temperature; available throughout the brew day. |
| `candidate_mash_temperature` | External/control-device temperature that may be selected for mash supervision. |
| `mash_temperature` | Logical selected BLE/process temperature exposed to BrewAssistant. |

Recommended metadata:

- `ba_source`: stable provider-specific source beginning with
  `rapt_brew_bridge_`;
- `ba_temperature_role`;
- `ba_sensor_role` where multiple logical sensors share a physical value;
- `raw_device_id` or another stable, non-secret device identifier;
- `observed_at`;
- `provider_observed_at`;
- `data_age_seconds`;
- `value_rejected` and `reject_reason` when a provider supplied an invalid
  candidate.

A missing, non-numeric or known sentinel external temperature must become
unavailable. It must not fall back to the BrewZilla internal temperature while
claiming the BLE role.

## Command contract

| Priority | Command | Confirmation rule |
|---|---|---|
| P0 | Set target temperature | Fresh observed target equals the requested value within defined precision. |
| P0 | Enable/disable heating | Fresh observed heating-enabled state matches. |
| P0 | Set heating utilisation | Fresh observed utilisation matches within defined precision. |
| P0 | Enable/disable pump | Fresh observed pump-enabled state matches. |
| P0 | Set pump utilisation | Fresh observed utilisation matches within defined precision. |
| P1 | Start profile/session | Fresh runtime exposes the intended profile and concrete session. |
| P1 | End profile/session | Fresh runtime no longer exposes an active profile/session. |

Commands must:

- validate device identity, capability and numeric range;
- fail clearly when unavailable or unsupported;
- avoid optimistic state updates unless Home Assistant explicitly represents
  them as pending;
- expose the requested action and subsequent observed result in diagnostics;
- use bounded retries;
- never issue compensating OFF/STOP commands solely because confirmation was
  not received.

## Profile runtime

Profile activity must not be inferred from a transient step ID alone. A profile
is active only when the new provider supplies enough evidence to identify a
concrete profile/session according to its own model.

The normalized runtime should include, when available:

- profile ID and name;
- session ID and start time;
- current step ID, number/order, name and target;
- control type, end type, duration type and length;
- PID, heating and pump settings relevant to the current step;
- next step ID, name and target;
- bounded normalized step list;
- completeness and freshness markers.

## Other device families

Extended compatibility targets include:

- RAPT Pill/hydrometer: gravity, temperature, battery and connection;
- temperature controller: temperature, target and connection/control
  capabilities;
- bonded devices: temperature, humidity, pressure, battery, connection and
  parent-device relationship.

These are P2 until the BrewZilla replacement path is safe unless BrewAssistant
introduces a direct dependency that raises their priority.

## BrewAssistant ownership rules

The external BLE/process sensor is owned by BrewAssistant from Heat strike
through Mash, Mash out, Sparge and Pre-boil. It is released when Boil starts.
During Chill and Transfer, the Cooling/CFC backend may use the same physical
sensor as wort-out temperature.

BrewZilla internal temperature remains the kettle temperature throughout the
brew day.

An active BrewZilla profile outranks Brewfather Brew Tracker as the process
runtime source, while Brewfather may still provide recipe metadata.

## Versioning

Any deliberate contract change must:

1. update this document;
2. add or update automated contract tests;
3. state whether BrewAssistant changes are required;
4. be validated on `dev`, promoted to `beta`, then released on `main`.
