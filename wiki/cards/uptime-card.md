# custom:uptime-card

HACS card rendering a row of colored bars representing an entity's state history (like Uptime Kuma's own bar chart, but inside HA).

## Key options seen in practice

- `entity:` — the sensor whose history is shown (one entity per card instance — no multi-entity support observed in this repo's usage).
- `hours_to_show:` — time window represented by the bars (this repo uses `48`).
- `ok:` — list of state values that count as "healthy"/green (e.g. `[up]`). Anything else is treated as down. Use this when your sensor reports a different healthy-state string than `up`.
- `alignment.tooltip_first: true` — tooltip positioning tweak.
- `show.header` / `show.footer` / `show.status` — toggle the card's own header/footer/status text; this repo sets all three `false` because that information is already shown by the paired [[cards/mushroom-template-card]] in the same [[cards/stack-in-card]].
- `bar.height`, `bar.round`, `bar.amount`, `bar.spacing` — visual tuning of the bar chart (bar count, corner radius, gap, height in px).

## Gotcha: positioning hack

The example repo applies a `card_mod` style with `position: relative; left: -67%; width: 150–165%;` to the uptime-card's `ha-card`. This is a deliberate hack to make the bar chart visually overflow/align correctly inside the `stack-in-card` horizontal split — see [[concepts/card-mod]] and [[troubleshooting/card-mod-overflow-hack]]. Don't "simplify" this away without checking the rendered layout; it's load-bearing.
