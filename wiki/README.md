# Wiki schema

This directory is a personal, LLM-maintained knowledge base for Home Assistant Lovelace dashboards, Jinja2 templating, and the HACS card ecosystem. It is general-purpose (not limited to this repo's dashboard) — this repo is just where it happens to live and where the first sources/pages came from.

Pattern: raw sources are immutable; the LLM reads them and writes/updates wiki pages; `index.md` and `log.md` are the navigation aids. See the parent conversation/idea doc for the full philosophy — this file just nails down the conventions for *this* instance.

## Layers

- `sources/` — raw, immutable inputs (clipped articles, HA docs pages, forum threads, your own notes-as-source). Never edited once added, only appended to. Each file should keep its original title/URL if known.
- `concepts/`, `cards/`, `entities/`, `troubleshooting/` — the wiki proper. LLM-owned, freely rewritten as understanding improves.
  - `concepts/` — cross-cutting ideas: Jinja2 templating patterns, Lovelace layout mechanics, HA template sensors/entities in general, `card_mod` CSS targeting, etc.
  - `cards/` — one page per HACS/custom card type (e.g. `mushroom-template-card.md`, `stack-in-card.md`, `uptime-card.md`). Document the options that actually matter, gotchas, and link to where it's used.
  - `entities/` — sensor/entity design patterns (template sensors, state semantics like `up`/`unknown`/`down`, naming conventions).
  - `troubleshooting/` — specific gotchas and their fixes/workarounds, one page per issue or a grouped page per card/concept once there are enough.
- `index.md` — catalog of every page in the wiki, grouped by the folders above, one line per page (link + summary). Update on every ingest or new page.
- `log.md` — append-only chronological record. Entry format: `## [YYYY-MM-DD] <ingest|query|lint> | <short title>`, with a couple bullet lines underneath.

## Conventions

- Pages are markdown, optionally with YAML frontmatter (`tags:`, `updated:`, `sources:`) if it becomes useful for Dataview-style queries — not required to start.
- Cross-link liberally with `[[wiki-style]]` or relative markdown links (Obsidian renders both) — e.g. a `cards/uptime-card.md` page should link to `concepts/card-mod.md` if it discusses styling.
- When a new source contradicts or updates an existing page, edit the page in place and note the change in `log.md` — don't just append a "correction" section forever; keep pages current, not archaeological.
- This repo's own files (`example.yml`, `ts-services_up-down.yml`) count as sources too — several starter pages below were seeded directly from them (see "Seeded from this repo" entries in `log.md`).

## Workflow

**Ingest**: drop a file in `sources/`, then ask the LLM to process it — it reads the source, proposes which wiki pages to create/update, makes the edits, updates `index.md`, and appends to `log.md`.

**Query**: ask a question; the LLM reads `index.md` first, drills into relevant pages, answers with citations to specific wiki pages (and sources where useful). Good answers (comparisons, synthesis) can be filed back as new pages — ask explicitly if you want that.

**Lint**: periodically ask the LLM to check for contradictions, stale claims, orphan pages (nothing links to them), and concepts mentioned-but-not-paged.
