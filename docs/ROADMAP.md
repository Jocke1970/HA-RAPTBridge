# Roadmap

## Release model

Development follows:

`dev` → `beta` → `main`

No physical-control release is promoted directly to `main`.

## Phase 0 — Preserve evidence and define the boundary

- [x] Create independent repository and MIT license.
- [x] Define architecture and compatibility contract.
- [x] Credit the original RAPT Cloud Link project and author.
- [ ] Save the legacy Swagger/OpenAPI specification while available.
- [ ] Capture sanitized legacy BrewZilla payload fixtures.
- [ ] Capture sanitized profile inactive/active/step-change/stopped fixtures.
- [ ] Inventory exact legacy entities, services, unique IDs and ranges.
- [ ] Record current BrewAssistant dependencies on `rapt_cloud_link`.

**Exit:** enough evidence exists to test a new implementation without querying
the old backend.

## Phase 1 — Discover the new RAPT Brew backend

- [ ] Locate official API documentation and access requirements.
- [ ] Confirm whether API access requires a subscription.
- [ ] Identify authentication, refresh and revocation behaviour.
- [ ] Identify device discovery and capability endpoints.
- [ ] Determine polling, event/push or hybrid transport.
- [ ] Map BrewZilla telemetry and freshness timestamps.
- [ ] Map commands without executing them on active equipment.
- [ ] Determine profile/session support.
- [ ] Document rate limits and expected update cadence.

**Exit:** a redacted, reproducible API map exists with no credentials committed.

## Phase 2 — Home Assistant read-only integration

- [ ] Scaffold `custom_components/rapt_brew_bridge`.
- [ ] Add manifest, config flow, translations and diagnostics.
- [ ] Implement authentication and reauthentication.
- [ ] Implement device discovery and canonical coordinator model.
- [ ] Expose BrewZilla internal temperature, target, connection and freshness.
- [ ] Expose heating/pump state and utilisation as read-only entities.
- [ ] Add fixtures and unit tests for missing, stale and malformed data.
- [ ] Add HACS metadata only after the integration loads cleanly.

**Exit:** side-by-side read-only values can be compared with the legacy
integration without collisions.

## Phase 3 — External temperature and profile runtime

- [ ] Map control-device/BLE temperature.
- [ ] Implement rejection and source diagnostics.
- [ ] Expose telemetry age and BLE-data age.
- [ ] Normalize active profile/session and current/next step.
- [ ] Validate start, step change and stop observations over fresh polls.
- [ ] Add bounded Home Assistant attributes and contract tests.

**Exit:** BrewAssistant can observe the complete brew-day state without sending
commands.

## Phase 4 — Supervised physical control

Implement and test one command at a time:

- [ ] target temperature;
- [ ] heating enabled;
- [ ] heating utilisation;
- [ ] pump enabled;
- [ ] pump utilisation;
- [ ] profile start;
- [ ] profile end.

For every command:

- [ ] validate ranges and capability;
- [ ] require explicit supervised confirmation during initial testing;
- [ ] verify through fresh observed state;
- [ ] test timeout and provider-loss behaviour;
- [ ] confirm that no automatic compensating OFF/STOP command is emitted.

**Exit:** each command has live evidence, automated tests and a documented
failure mode.

## Phase 5 — BrewAssistant provider migration

- [ ] Add a provider adapter accepting legacy and new sources.
- [ ] Make provider selection explicit and reversible.
- [ ] Compare both providers during a supervised brew day.
- [ ] Verify temperature ownership through Boil/Chill/Transfer.
- [ ] Verify active-profile precedence over Brewfather runtime.
- [ ] Verify fail-closed behaviour on API loss.
- [ ] Document cutover and rollback.

**Exit:** BrewAssistant can switch providers without changing recipe/runtime
logic.

## Phase 6 — Prerelease and stable release

- [ ] Publish first `beta` prerelease.
- [ ] Validate installation, reauthentication and upgrades through HACS.
- [ ] Complete English and Swedish translations.
- [ ] Add user diagnostics and issue templates.
- [ ] Publish migration guide from legacy RAPT Cloud Link.
- [ ] Promote to `main` only after supervised physical validation.

## Explicit non-goals for the first release

- fully autonomous direct control;
- undocumented background command retries;
- preserving every legacy debug attribute;
- replacing BrewAssistant recipe/runtime logic;
- claiming support for device families that have not been tested.
