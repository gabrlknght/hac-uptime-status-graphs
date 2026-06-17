# HA template sensors (aggregation pattern)

Home Assistant's `template:` integration (modern syntax, replaces legacy `sensor: - platform: template`) lets you define a `sensor:` whose `state:` is a Jinja expression over other entities' states.

## Pattern used in this repo

[ts-services_up-down.yml](../../ts-services_up-down.yml) defines one sensor, `Services All Up` (`sensor.services_all_up`), that loops a hardcoded list of service status entities and reduces them to a single `up`/`unknown`/`down` state:

```jinja
{% set services = [...] %}
{% set ns = namespace(any_down = false, any_unknown = false) %}
{% for service in services %}
  {% if is_state(service, 'unknown') %}
    {% set ns.any_unknown = true %}
  {% elif not is_state(service, 'up') %}
    {% set ns.any_down = true %}
  {% endif %}
{% endfor %}
{% if ns.any_down %}down{% elif ns.any_unknown %}unknown{% else %}up{% endif %}
```

This is a fan-in: many leaf sensors → one summary sensor, consumed by the dashboard's summary tile (see [[cards/mushroom-template-card]] usage in `example.yml`). It mirrors the Lovelace-side namespace pattern in [[concepts/jinja-in-lovelace]], but now produces the same three-way state instead of a boolean — see [[entities/status-semantics]] for why the asymmetry was fixed.

## Maintenance note

The `services` list here must stay in sync with the per-service cards in `example.yml` (or the `services` list inside `example-auto-entities.yml`'s `filter.template` — see [[cards/auto-entities]]) — adding/removing a service means editing both files. See the root `CLAUDE.md` "Making changes" section for the checklist.
