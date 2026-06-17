# Why `namespace()` instead of plain `{% set %}` in loops/if-blocks

Jinja2's `{% set x = ... %}` creates a variable scoped to the current block. Inside a `{% for %}` loop or `{% if %}` block, a `{% set %}` does NOT persist its updated value to an outer scope once the block ends — each iteration/branch gets its own scope.

`namespace()` (from Jinja2's `jinja2.utils.Namespace`) creates a mutable object whose attributes CAN be reassigned from inside nested blocks and have that change visible outside. This is the standard Jinja workaround for "accumulator across a loop" patterns.

Used twice in this repo:
- [[entities/template-sensors]] — `ns.all_up` flips to `false` inside a `{% for %}` / `{% if %}`, and the final state check happens after the loop.
- [[concepts/jinja-in-lovelace]] — `ns.down` accumulates a list of human-readable service names across multiple independent `{% if %}` blocks (not a loop, since each service has a literal hardcoded check).

Without `namespace()`, both patterns would silently fail to accumulate — `ns.all_up` would always read as its initial value outside the loop.
