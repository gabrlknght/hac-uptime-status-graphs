# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A distributable Home Assistant Lovelace dashboard config (YAML only, no build/test tooling). It renders a per-service uptime status board: a summary tile ("Everything's Online" / "Something's Wrong") plus per-service status tiles paired with historical uptime bars, sourced from Uptime Kuma sensors.

There is no code to build, lint, or test — this is a config repo. Validation happens by pasting the YAML into a Home Assistant Lovelace dashboard (raw config editor) and checking it renders/templates correctly.

## File roles

- `ts-services_up-down.yml` — a Home Assistant `template:` sensor (`Services All Up`) that aggregates a hardcoded list of `sensor.service_*_status` entities into a single `up`/`unknown`/`down` state (down takes priority if any service is explicitly down; unknown only when no service is down but at least one hasn't reported yet). Goes in HA's `configuration.yml` under sensors/templates. This is the data layer the dashboard reads from.
- `example.yml` — the hand-written Lovelace `vertical-stack` card layout: one summary `mushroom-template-card` driven by `sensor.services_all_up`, followed by per-service rows. Each row is a `horizontal-stack` of two `custom:stack-in-card` blocks, each pairing a `mushroom-template-card` (status tile) with a `custom:uptime-card` (history bar) for one service. This is the proven, dependency-light presentation layer — every service block is explicit YAML.
- `example-auto-entities.yml` — an alternative presentation layer using the HACS `custom:auto-entities` card with `filter.include` (one literal entry per service, each carrying its own `type: custom:stack-in-card`) instead of manually pairing services into `horizontal-stack` rows — `auto-entities`' `grid` host auto-flows them. Each service's Jinja is still written out in full, same as `example.yml` (`auto-entities` does not support generating differently-parameterized full nested cards from a single Jinja loop in `filter.template` — see `wiki/cards/auto-entities.md` for why an earlier version of this file assumed otherwise and was wrong). The only real savings vs. `example.yml` is skipping the `horizontal-stack` row-pairing boilerplate.
- `README.md` — install/usage instructions aimed at end users (HACS dependencies, placeholder replacement steps). Treat it as the source of truth for setup steps and prerequisites; keep it in sync when changing placeholder structure.

## Architecture pattern to preserve when editing

Both layout files use the same placeholder naming convention and must stay consistent with `ts-services_up-down.yml`:
- Entity IDs follow `sensor.service_<letter>_status` (a–h in the example), matching between `ts-services_up-down.yml`'s `services` list and the layout file's per-card `entity:` references / Jinja conditionals. This applies the same way in both `example.yml` and `example-auto-entities.yml` — both hand-write the same per-service Jinja, just in different containers (`cards:` vs. `filter.include`).
- In both layout files, each service block is fully self-contained and repetitive by design (no YAML anchors/templating) — each `mushroom-template-card` + `custom:uptime-card` pair duplicates the same Jinja logic with only the entity ID, icon, and a couple of `card_mod` CSS values varying. When adding a new service, copy an existing pair block and update both files (the template sensor's `services` list AND a new card block) together.
- Status semantics: sensors report `up` / `unknown` / anything else (treated as down/offline). Per-service tiles and the summary sensor both treat `unknown` as distinct from `down` — don't collapse `unknown` into `down` again, that reintroduces false "Something's Wrong" alerts during HA restarts before Uptime Kuma sensors repopulate.
- `card_mod` styling (transparent borders, custom widths/positioning like `left: -67%`) is load-bearing layout hackery for the horizontal stack-in-card visual — don't simplify it away without checking the rendered layout.

## Wiki

`wiki/` is a personal, LLM-maintained knowledge base for HA Lovelace/Jinja/HACS-card knowledge in general (not limited to this repo) — see `wiki/README.md` for the schema and `wiki/index.md` for the page catalog. When you learn something non-obvious about a card, Jinja pattern, or HA entity behavior while working in this repo, consider filing it into the wiki (and updating `wiki/index.md` / `wiki/log.md`) rather than letting it live only in chat history.

## Making changes

- Keep entity ID placeholders generic (`sensor.service_X_status`) since this is a public example repo meant to be copy-pasted and customized by users — don't hardcode real hostnames/sensor names.
- When adding/removing a service in either layout file, update three places: the `services` list in `ts-services_up-down.yml`, the summary card's `entities`/down-list dict, and add/remove a `stack-in-card` block (under `cards:` in `example.yml`, under `filter.include` in `example-auto-entities.yml`).
- License is MIT (see `LICENSE`); README reflects this — don't reintroduce Apache-2.0 references.
