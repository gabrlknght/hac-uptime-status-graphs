# card_mod

HACS module that lets any Lovelace card accept a `card_mod:` block of raw CSS, scoped to that card's shadow DOM. Two forms seen in this repo:

```yaml
card_mod:
  style: |
    ha-card { ... }
```

— a single CSS block targeting the card's own `ha-card` root.

```yaml
card_mod:
  style:
    .: |
      ha-card { ... }
    mushroom-state-info$: |
      .primary, .secondary { ... }
```

— a map form where each key is a CSS selector (`.` = the card itself) and `$` suffix means "pierce into this child component's shadow DOM" (needed because Mushroom's internal `mushroom-state-info` element has its own shadow root that normal CSS can't reach).

## Patterns used in this repo

- Stripping borders/shadows: `border-color: transparent; box-shadow: none;` — used to make stacked cards look like one merged unit (see [[cards/stack-in-card]]).
- Forcing text wrap: `white-space: normal !important; overflow: visible !important; text-overflow: clip !important;` on `.primary`/`.secondary` inside `mushroom-state-info$` — prevents long service names/status text from being clipped/ellipsized.
- Icon scaling: `transform: scale(2)` plus explicit width/height on `ha-icon`/`.icon` — used on the summary tile to make its icon visually larger than default.
- Layout overflow hack: `position: relative; left: -N%; width: M%; overflow: visible !important;` on an `ha-card` — see [[troubleshooting/card-mod-overflow-hack]].

## Gotcha

`$`-suffixed selectors only work if the target element actually uses Shadow DOM and card_mod knows how to pierce it. If a style isn't applying, check whether the target needs the plain `.` form instead, or a different child selector.
