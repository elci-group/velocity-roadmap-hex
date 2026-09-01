# Roadmap: Replace `hex` in Velocity

## Status

**Draft** — awaiting prioritization.

## Bottom line

`hex` is used in exactly one crate (`velocity-registry`) for hex encoding/decoding. The usage is small enough to be replaced with an internal module, eliminating a CVE-flagged dependency with almost no risk.

This is the easiest of the three roadmaps and a good first migration.

## Quick facts

| | |
|---|---|
| Crate | `hex` v0.4.3 |
| Amber recommendation | `propose` |
| Amber classification | Low Risk |
| Affected files | 1 |
| Call sites | 4 |
| Unique APIs used | ~2 (`hex::encode`, `hex::decode` or similar) |

## Documents

- [`ROADMAP.md`](./ROADMAP.md) — detailed migration plan, risks, and acceptance criteria.
