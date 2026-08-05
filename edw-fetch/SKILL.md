---
name: edw-fetch
description: How to pull ONE BATCH of personnel and household records from the Enterprise Data Warehouse — carrying provenance and as-of, and flagging stale snapshots. For a feed that must stay current, use continuous-feed instead.
---

# Fetch from the Enterprise Data Warehouse

> **This is the one-off path.** If the officer wants the picture to *stay current*
> — "keep it current", "not a one-off", "get that flowing" — stop here and use
> [continuous-feed](../continuous-feed): a single `muster_setup_etl` call. Do not
> assemble the batch chain for a continuous ask.

The EDW is the system of record for who works where and which household they
belong to. Read it through the **edw connector** — never scrape it directly.

## What to pull
- **Personnel:** person id, name, station, location, muster status, clearance band.
- **Households:** the household each person belongs to (the key that later joins
  dependents and pets).

## Rules
1. **Every record carries its origin.** Keep `system = EDW`, the warehouse
   `record_id`, and the `as_of` timestamp on everything you fetch. Downstream
   steps depend on it.
2. **The warehouse is a nightly snapshot.** If `as_of` is old, flag it stale.
   A warehouse read is one source among several — never treat it as ground truth
   on its own.
3. **Fetch the delta, not the world.** For a recurring sync, pull only what has
   changed since the last run so the ingest stays small and cheap.

This skill is the first block of the **one-off** EDW → Pets → Quine batch sync. It
hands its records to [pets-and-dependents](../pets-and-dependents) and
[quine-ingest](../quine-ingest).

## For a one-off arrivals batch
Call the **`edw_get_arrivals`** tool (on the edw-connector). It returns a small
**`batch_id`** and stages the batch server-side. Pass that `batch_id` to the
pets-and-dependents skill next — do not try to pass the records yourself.
