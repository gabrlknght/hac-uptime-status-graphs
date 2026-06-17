# custom:stack-in-card

A HACS card that visually merges multiple child cards into one card-like container (single border/background) instead of showing them as separate cards. `mode: horizontal` lays children left-to-right; `mode: vertical` (default) stacks them.

## Pattern used in this repo

Each "service row" in [example.yml](../../example.yml) wraps exactly two cards in one `stack-in-card` with `mode: horizontal`:
1. A [[cards/mushroom-template-card]] showing the status tile (icon, name, online/offline text).
2. A [[cards/uptime-card]] showing the historical bar graph.

Two of these `stack-in-card` blocks are then placed side by side in an outer `horizontal-stack`, giving a 2x-services-per-row grid.

## Why it's used here instead of a plain horizontal-stack

`horizontal-stack` gives each child its own card chrome (border/shadow/background), which looks wrong when you want a tile and a graph to look like one unit. `stack-in-card` merges the chrome into a single card, then per-child `card_mod` styling (see [[concepts/card-mod]]) strips borders on the children so only the outer merged card has visible chrome.
