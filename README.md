# uptime-status-graphs

![Uptime Status Card](./Uptime%20Status%20Card.png)

A compact Home Assistant Lovelace layout that provides service status tiles and uptime graphs. Built from Mushroom template cards, `stack-in-card`, and `uptime-card`, this example dashboard is easy to adapt for your own sensors.

Repository: `ha-customs/uptime-status-graphs`

License: Apache-2.0 (permissive)

---

## What this is

A small, opinionated Lovelace layout that:
- Shows a summary tile that reports “Everything's Online” or “Something's Wrong”.
- Presents per-service status tiles and uptime graphs.
- Uses Mushroom template cards for concise visuals and `uptime-card` for historical uptime bars.

This repo contains:
- `example.yaml` — a sanitized, copy‑ready Lovelace snippet using placeholder sensor IDs.
- `README.md` — this guide.

## Prerequisites

Install the following custom cards (HACS recommended) before using the example YAML:
- mushroom (mushroom-template-card, mushroom-title-card)
- custom:stack-in-card
- custom:uptime-card

Recommended Home Assistant version: latest stable release.

## Install (Manual)

1. Install the dependencies above via HACS (recommended) or add them manually to `www/community/`.
2. Open your Lovelace dashboard (Edit). Choose manual card and paste the contents of `example.yaml` (or use `raw config editor`).
3. Replace the placeholder sensor entity IDs with your own.

## Configuration

This example uses a single `services` array in the templates. Replace placeholders with your actual status sensors. Each status sensor should expose a state that the templates can interpret (the example expects `up` for healthy services). If your sensors use different states, adapt the templates accordingly.

Placeholders used:
- `sensor.service_1_status`, `sensor.service_2_status`, ...

Default icons used in the example are Material Design Icons (mdi:). We recommend using mdi names that are already available in Home Assistant.

## Customization

- Icons: change the `icon:` property per card (e.g., `mdi:home-assistant`, `mdi:plex`).
- Colors: edit `badge_color` and `icon_color` values. If you want dynamic color controls, create an `input_select` or `input_text` helper and reference its state in the templates.

Example color helper (optional):
```yaml
# Create an input_select in your helpers with values [green, red, orange, blue]
# Then reference it in templates (pseudo):
badge_color: "{{ states('input_select.status_color') }}"
```

## Security / Privacy

This example contains only entity IDs and no credentials or hostnames. Before publishing, ensure you have replaced any site‑specific or private values.

## Example usage

1. Copy `example.yaml` into a Lovelace manual card.
2. Replace the `sensor.service_*_status` placeholders with your sensors.
3. Optionally adjust `hours_to_show` and `bar.amount` in the `uptime-card` sections.
4. Use ok=['up'] or an equivalent setting to help translate the correct bar values as 'on' if your sensor reports back a different status value.

## Files
- `example.yaml` — sanitized Lovelace YAML with placeholders.

## Credits
Built with Mushroom, `stack-in-card`, and `uptime-card`.

## License
Apache-2.0 — see the official license text for terms.

---

If you want, I can:
- Add a short section showing how to wire an `input_select` for dynamic colors.
- Create a small screenshot guide (placeholders provided) and commit both files to a new `ha-customs/uptime-status-graphs` repo folder.

Tell me which next step you prefer.