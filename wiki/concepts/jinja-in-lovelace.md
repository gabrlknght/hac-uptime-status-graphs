# Jinja2 in Lovelace YAML

Several custom cards (Mushroom in particular) accept Jinja2 templates in string fields (`primary`, `secondary`, `icon`, `icon_color`, `badge_icon`, `badge_color`, etc.), evaluated by Home Assistant's templating engine, re-rendered when a referenced entity's state changes.

## Patterns used in this repo

**Simple ternary via if/else:**
```jinja
{% if is_state('sensor.x', 'up') %} Online {% else %} Offline {% endif %}
```

**Three-way branch** (`up` / `unknown` / else-as-down) for per-service status text and badges — see any service block in [example.yml](../../example.yml).

**Namespace accumulator pattern** — used in the summary tile's `secondary` field to build "Down: A, B, C" / "Unknown: D" lists. **Refactored 2026-06-17** from 8 hand-written `{% if %}` blocks (one per service, the original approach) to a loop over a `{entity: label}` dict, matching the pattern already used in [[entities/template-sensors]]:
```jinja
{% set services = {'sensor.service_a_status': 'Service A', ...} %}
{% set ns = namespace(down=[], unknown=[]) %}
{% for entity, label in services.items() %}
  {% if is_state(entity, 'unknown') %}
    {% set ns.unknown = ns.unknown + [label] %}
  {% elif not is_state(entity, 'up') %}
    {% set ns.down = ns.down + [label] %}
  {% endif %}
{% endfor %}
Down: {{ ns.down | join(', ') }}
```
`namespace()` is required here because plain `{% set %}` variables don't persist mutations across `{% if %}` blocks/loops in Jinja — see [[troubleshooting/jinja-namespace-why]]. The loop form means adding a service to the summary's down/unknown list is one dict entry instead of one new `{% if %}` block.

**YAML block scalars for multi-line templates** — `>` (folded, used for `secondary`/`primary` where embedded newlines should collapse to spaces) vs `|` (literal, used for `icon`/`color`/`badge_icon` where the template is short and exact whitespace doesn't matter as much). Mixing the wrong style can introduce unwanted newlines or spacing in rendered text.

## Where the template sensor differs

[[entities/template-sensors]] (`ts-services_up-down.yml`) uses Jinja inside `configuration.yml`, evaluated by HA's `template:` integration to produce a sensor *state*, not inside Lovelace card config. Same Jinja dialect, different host — the `namespace`/loop pattern there iterates a list of entity IDs rather than hand-writing one `{% if %}` per service, since it only needs a boolean, not a human-readable list.
