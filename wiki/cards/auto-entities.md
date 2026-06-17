# custom:auto-entities

HACS card that generates a card's `entities:` (or `cards:`, for grid/stack-type host cards) list dynamically, instead of you writing it by hand. Configured with a `card:` (the host card type that receives the generated list) and a `filter:` (how to produce the list).

## What it actually supports (confirmed from source, 2026-06-17)

From the upstream README's "How it works" section: `auto-entities` builds a list of **entities** (from `entities:`, `filter.template`, and `filter.include`/`exclude`), then "creates a card based on the configuration given in `card:`, and fills in `entities:` of that card with the entities from above," where each entity is normally of the form `- entity: <entity_id>` plus options.

There is exactly one escape hatch from the "entity" shape: a literal entry under **`filter.include`** that includes its own `type:` key is, per the docs, "handled as a complete entity description and passed along directly to the card" — i.e. it skips entity-matching entirely and gets passed straight into the generated list as-is. **This `type:`-passthrough is documented as a feature of literal `filter.include` entries — not of `filter.template`'s rendered output.**

## A wrong assumption this repo made, and corrected (2026-06-17)

The first attempt at [[cards/auto-entities]]-driven generation assumed `filter.template` could emit a Jinja-built list of **arbitrary full nested card objects** (each with its own `type: custom:stack-in-card`, looping over services, computing status/icon/color per iteration) and that `auto-entities` would pass each one through unchanged because it had a `type:` key — generalizing the `filter.include` passthrough rule to `filter.template` too. That assumption was never confirmed against the docs and was **wrong**.

Symptom this produced: the `filter.template` itself rendered perfectly (verified clean, valid JSON via Developer Tools → Template, both as hand-built JSON text and later as a single `{{ ... | to_json }}` expression) — but the live dashboard showed "Configuration error" for every single generated card, and clicking the error tiles showed no further detail (consistent with `auto-entities` rejecting each item before it ever reaches card-level validation, because it expected an `entity:` key that wasn't there). Two rounds of suspecting the Jinja itself (a `tojson`/`to_json` filter-name bug, then a hand-built-JSON-vs-single-`to_json` rewrite) didn't fix it, because the real issue was architectural, not a template syntax bug.

## What actually works: `filter.include` with literal `type:` entries

Since the `type:`-passthrough is real but scoped to `filter.include`, the working pattern is to list each service as a **literal YAML entry** under `filter.include`, each carrying its own `type: custom:stack-in-card` and the same per-service Jinja (`is_state('sensor.x', 'up')` etc., hardcoded per entity — same as [[concepts/jinja-in-lovelace]]) that [[cards/mushroom-template-card]] always needed anyway:

```yaml
type: custom:auto-entities
card:
  type: grid
  columns: 2
  square: false
card_param: cards
filter:
  include:
    - type: custom:stack-in-card
      mode: horizontal
      cards: [...]   # mushroom-template-card + uptime-card, same Jinja as example.yml
    - type: custom:stack-in-card
      ...             # one such entry per service
```

`card_param: cards` still applies the same way — it tells `auto-entities` which key on the host card (`type: grid` here) receives the assembled list, regardless of whether that list came from `template` or `include`.

## The actual benefit this provides (smaller than originally hoped)

This does **not** collapse the per-service Jinja duplication — each service's `mushroom-template-card`/`uptime-card` pair is still fully written out, same as [[cards/stack-in-card]] in `example.yml`. The only real savings: `grid` auto-flows the cards into columns, so you don't need to manually pair services into `horizontal-stack` rows of two. If true "one Jinja loop generates N differently-parameterized full cards" is still wanted, `auto-entities` is not the right tool for it — that would need something purpose-built for template→card generation (e.g. a card-templater-style tool), which hasn't been investigated or verified here.

## Status

Confirmed working pattern as of 2026-06-17: `filter.include` with literal per-service `type:` entries, YAML-validated with `python3 -c "import yaml; yaml.safe_load(...)"` before pasting into Lovelace. Still pending live confirmation in the user's actual Home Assistant instance (validated structurally, not yet rendered live) — and once it renders, double-check the `card_mod` overflow hack (see [[troubleshooting/card-mod-overflow-hack]]) still looks right inside the `grid` layout, which replaced the manual `horizontal-stack` wrapper from `example.yml`.
