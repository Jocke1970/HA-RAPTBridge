# RAPT backend research notes

Last reviewed: 2026-11-18.

## Verified public information

- RAPT documentation states that migration of all RAPT devices to a new backend
  architecture began on **2026-09-21**.
- The legacy RAPT portal at `app.rapt.io` is scheduled for deprecation on
  **2026-12-31**.
- The legacy Swagger UI at `api.rapt.io` was still reachable when this project
  was initialized.
- Current RAPT Brew marketing states "Full API access and one-tap data export",
  but a complete public replacement API contract has not yet been identified
  by this project.

Public references:

- <https://docs.rapt.io/>
- <https://rapt.io/features>
- <https://api.rapt.io/index.html>
- <https://rapt.io/blog/new-rapt-app-now-live>
- <https://rapt.io/blog/rapt-fermentation-control-and-big-news>

## Important distinction

Deprecation of the legacy portal does not, by itself, prove the exact shutdown
date of every legacy API endpoint. However, device and account migration to a
new backend can make the legacy API unusable for migrated devices even while
the old endpoint remains reachable.

The implementation must therefore be based on observed and documented new
backend behaviour, not on an assumed one-to-one URL replacement.

## Unknowns to resolve

- Where is the supported API documentation for the new backend?
- Is API access free, premium or tiered?
- Which authentication mechanism is supported for third-party integrations?
- Are refresh tokens or device-bound credentials used?
- Does the backend provide push events, polling endpoints or both?
- Which timestamps represent device observation, backend receipt and API
  response time?
- Are BrewZilla commands officially supported?
- Are active profiles/automations and profile sessions exposed?
- Are RAPT BLE thermometers standalone devices, bonded children or embedded
  BrewZilla control-device values?
- What are the rate limits and command idempotency rules?
- Can legacy and new device registrations coexist during testing?

## Evidence collection rules

- Never commit usernames, passwords, API secrets, bearer tokens, cookies,
  device authentication tokens or unredacted request headers.
- Replace account and device identifiers consistently in fixtures so
  relationships remain testable.
- Preserve field names, data types, null/zero/false distinctions and timestamp
  formats.
- Record the action that caused each payload: idle, heating, pumping, active
  profile, step change and stopped profile.
- Record both request acknowledgement and the later observed device state.
- Store sanitized fixtures under `tests/fixtures/` only after manual review.

## Suggested discovery record

For each endpoint or event, record:

| Field | Description |
|---|---|
| Purpose | Discovery, telemetry, command or profile runtime |
| Method / transport | GET, POST, WebSocket, SSE, MQTT or other |
| Authentication | Redacted auth type, never credentials |
| Request shape | Sanitized parameters/body |
| Response shape | Sanitized fixture path |
| Provider timestamp | Field and timezone/format |
| Device timestamp | Field and timezone/format |
| Error behaviour | Status/body and retry guidance |
| Rate limit | Headers or observed restriction |
| Confidence | Official, observed, inferred or unknown |
| Last verified | Date and firmware/app version |
