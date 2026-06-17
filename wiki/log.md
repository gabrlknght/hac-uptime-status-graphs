# Wiki log

Append-only. Format: `## [YYYY-MM-DD] <ingest|query|lint> | <short title>`, then a couple of bullet lines.

## [2026-06-17] ingest | Seeded from this repo's existing files
- No new external source; bootstrapped the wiki by extracting documented behavior directly from `example.yml` and `ts-services_up-down.yml`.
- Created: `cards/mushroom-template-card.md`, `cards/stack-in-card.md`, `cards/uptime-card.md`, `concepts/card-mod.md`, `concepts/jinja-in-lovelace.md`, `entities/template-sensors.md`, `entities/status-semantics.md`, `troubleshooting/card-mod-overflow-hack.md`, `troubleshooting/jinja-namespace-why.md`.
- Open question flagged: the `card-mod-overflow-hack` page's explanation is inferred, not confirmed against a live-rendered dashboard — verify next time HA is available.

## [2026-06-17] query | Improvement analysis → 3 fixes implemented
- Reviewing the freshly-built wiki surfaced three actionable issues: (1) the summary tile's down-list used 8 hand-written `{% if %}` blocks instead of the loop pattern already used in the template sensor, (2) the template sensor collapsed `unknown` into `down`, causing false "Something's Wrong" alerts on HA restart, (3) the 8 hand-copied per-service card blocks were a real maintenance bottleneck (3 files to edit per service).
- Fixed #1 and #2 directly in `ts-services_up-down.yml` and `example.yml`: summary sensor and tile now produce/handle three states (`up`/`unknown`/`down`), down-list/unknown-list built via a loop over a `{entity: label}` dict.
- Implemented #3 as a new alternative file, `example-auto-entities.yml`, using the HACS `custom:auto-entities` card with `filter.template` to generate all per-service card blocks from one `services` list. Computes status/icon/color via `is_state()` in the outer template and emits via `tojson` to avoid nested-Jinja raw-block escaping. **Not yet verified live** — flagged in `CLAUDE.md`, `README.md`, and the new `cards/auto-entities.md` page.
- Updated pages: `entities/status-semantics.md`, `entities/template-sensors.md`, `concepts/jinja-in-lovelace.md`. New page: `cards/auto-entities.md`.

## [2026-06-17] lint | auto-entities approach was architecturally wrong, corrected
- Live testing of `example-auto-entities.yml`/`personal/dashboard.yml` kept failing ("Configuration error" on every generated card, no detail on click) even after the template was confirmed to render clean, valid JSON in Developer Tools → Template — twice (once as hand-built JSON via a `tojson`→`to_json` filter-name fix, once as a single `{{ ... | to_json }}` expression). Both fixes addressed symptoms, not the cause.
- Root cause found by reading the actual upstream README (fetched and indexed, not assumed): `auto-entities` builds a list of **entities** (`entity: <id>` + options); the `type:`-key passthrough that lets an item skip entity-matching and pass through as a complete object is documented as a feature of literal `filter.include` entries, not of `filter.template`'s rendered output. The original design (one Jinja loop in `filter.template` emitting 8 differently-parameterized full nested card objects) was never a supported `auto-entities` pattern — this was an unverified assumption made early in the work, and it cost two debugging rounds before being checked against the source.
- Corrected both files to use `filter.include` with 8 literal entries (each carrying its own `type: custom:stack-in-card`), restoring the same proven per-service Jinja `example.yml` always used. YAML-validated with `python3 -c "import yaml; yaml.safe_load(...)"` before being handed back for live testing.
- Lesson for this wiki: the real reduction-in-duplication goal of "#3" (see prior entry) was not actually achieved by `auto-entities` — it only saves the `horizontal-stack` row-pairing boilerplate, not the per-service Jinja repetition. Recorded honestly in `cards/auto-entities.md` rather than overstating the win.
- Updated: `cards/auto-entities.md` (full rewrite), `index.md`.
