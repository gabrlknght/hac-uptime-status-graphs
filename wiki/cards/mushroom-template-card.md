# mushroom-template-card

Part of the [Mushroom](https://github.com/piitaya/lovelace-mushroom) HACS card set. A flexible tile card where `primary`, `secondary`, `icon`, `icon_color`, `badge_icon`, and `badge_color` can all be Jinja templates, not just static entity lookups.

## Key options seen in practice

- `entity:` — the entity the card is "about"; also implicitly available to templates without `states('...')` if you use the short form, though this repo's examples always use explicit `is_state('sensor.x', 'up')` for clarity.
- `entities:` (plural) — extra entities the card should track/re-render on, even if not referenced via `entity:`. Needed when a template reads multiple entities' states (see `concepts/jinja-in-lovelace.md`) — otherwise the card won't re-render when those other entities change.
- `primary` / `secondary` — main text and subtext; both accept multi-line Jinja blocks (`>` YAML block scalar).
- `icon` / `icon_color` vs `badge_icon` / `badge_color` — the card supports both a main icon and a small overlay badge icon, each independently colorable/templatable. Used in this repo's dashboard to show a big status icon plus a small check/help/close badge.
- `fill_container: true` — stretches the card to fill its parent (used inside `stack-in-card` so the tile fills its half of the horizontal split).
- `layout: horizontal` — icon-left, text-right layout instead of stacked.
- `tap_action` / `hold_action` / `double_tap_action` — standard HA action maps. `action: more-info` opens the entity's detail dialog; `action: none` disables the interaction.

## Gotcha

If a template references entities other than the one in `entity:`, list them in `entities:` too, or the card may not refresh when those entities' states change. See [[entities/template-sensors]] for the aggregation pattern this is usually paired with.
