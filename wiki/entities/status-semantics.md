# Status sensor state semantics

Convention used throughout this dashboard for any `sensor.service_*_status` entity (typically backed by an Uptime Kuma monitor exposed to HA):

| State | Meaning | Treated as |
|---|---|---|
| `up` | Healthy | green / online |
| `unknown` | HA hasn't gotten a value yet (startup, integration delay) | orange / "Unknown" — distinct from down |
| anything else | Monitor reports the service is unreachable | red / offline / down |

This three-way split (not just up/down) is why the per-service Jinja in [[concepts/jinja-in-lovelace]] always has three branches, not a simple if/else — treating `unknown` as `down` would cause false "Something's Wrong" alerts during HA restarts before Uptime Kuma sensors repopulate.

**Fixed 2026-06-17:** the summary aggregation in [[entities/template-sensors]] used to be coarser — it treated anything-not-`up` (including `unknown`) as `down`, causing exactly that false-alarm-on-restart behavior. It now mirrors the per-service three-way split: `sensor.services_all_up` is `down` if any service is explicitly down, `unknown` only if no service is down but at least one hasn't reported yet, and `up` otherwise. Down takes priority over unknown (a real outage should never be masked by an unrelated unknown sensor).

If your own monitor reports a different "healthy" string than `up`, the [[cards/uptime-card]] `ok:` list lets you remap per-card without touching the underlying sensor.
