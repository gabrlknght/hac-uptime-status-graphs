# Why uptime-card uses `left: -67%` / `width: 150%`

Observed in [example.yml](../../example.yml): every `custom:uptime-card` inside a horizontal `stack-in-card` gets a `card_mod` style like:

```css
ha-card {
  position: relative;
  top: 0px;
  left: -67%;
  overflow: visible !important;
  width: 150%; /* or 165% on the first service block */
  border-color: transparent;
}
```

## Why (inferred, not yet confirmed against a live HA instance)

`stack-in-card` in horizontal mode splits its container evenly between children (50/50 for two children). The `uptime-card`'s bar chart wants more horizontal room than half the row to render `bar.amount: 24` bars at `bar.height: 80` / `bar.spacing: 10` legibly. The negative `left` offset combined with `width > 100%` and `overflow: visible` lets the card visually spill leftward into/past its allotted half without actually resizing the grid — i.e. a "render bigger than your box, then shift left to recenter" trick.

The first service block (Service A) uses `165%` while the rest use `150%` — likely a leftover inconsistency rather than an intentional difference; worth checking visually if you copy this block for a new service (see [[cards/uptime-card]]).

## Status

Open question — not yet verified live in a Home Assistant dashboard. Flag for follow-up next time the dashboard is actually rendered/tested.
