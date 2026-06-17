# Wiki index

Catalog of every page. See [README.md](README.md) for conventions. Update this on every ingest/new page.

## Concepts
- [card-mod](concepts/card-mod.md) — CSS injection into Lovelace cards; selector forms, `$`-piercing, patterns used in this dashboard.
- [jinja-in-lovelace](concepts/jinja-in-lovelace.md) — Jinja2 templating inside Lovelace card fields; ternary/three-way branches, namespace accumulator, block scalar styles.

## Cards
- [mushroom-template-card](cards/mushroom-template-card.md) — Mushroom's flexible templated tile card; key options and the `entities:` re-render gotcha.
- [stack-in-card](cards/stack-in-card.md) — merges multiple cards into one visual card; horizontal-mode pattern used to pair a status tile with a graph.
- [uptime-card](cards/uptime-card.md) — bar-chart history card; key options and the card_mod positioning hack.
- [auto-entities](cards/auto-entities.md) — what it actually supports (entity lists + literal `filter.include` type-passthrough, NOT arbitrary full cards from `filter.template`); corrects an earlier wrong assumption in this wiki.

## Entities
- [template-sensors](entities/template-sensors.md) — HA `template:` sensor fan-in pattern (many service sensors → one summary sensor); now produces `up`/`unknown`/`down`.
- [status-semantics](entities/status-semantics.md) — the `up` / `unknown` / down three-state convention; the summary-sensor asymmetry that caused false restart alarms was fixed 2026-06-17.

## Troubleshooting
- [card-mod-overflow-hack](troubleshooting/card-mod-overflow-hack.md) — why uptime-card uses negative `left` / `width > 100%`; unconfirmed live, flagged for follow-up.
- [jinja-namespace-why](troubleshooting/jinja-namespace-why.md) — why `namespace()` is required instead of plain `{% set %}` across loops/if-blocks.

## Sources
(none ingested yet — drop files in `sources/` and ask the LLM to process them)
