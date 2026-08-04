---
name: quine-ingest
description: How to ingest source records into the living accountability graph as event-sourced facts — deterministic ids, value-keyed, never overwrite.
---

# Ingest into the Living Graph (Muster-Quine)

The accountability graph is **never written by hand.** Every change enters as an
event, through the **Muster-Quine connector's ingest tool**. This is what makes
the picture auditable: you can always ask where a value came from and when.

## Rules
1. **Deterministic ids.** Derive each node/fact id from the record's stable keys
   (`idFrom`) so re-ingesting the same record **merges** instead of duplicating —
   the ingest is safe to replay and safe to run every five minutes.
2. **Facts are additive. Never overwrite, never delete.** A correction is a *new*
   fact; the prior fact stays for the audit trail. The graph keeps every version.
3. **Every fact carries its source and age** — the `system` it came from and its
   `as_of`. A value with no time behind it is a rumor.
4. **Value-keyed facts** so identical values dedupe and disagreements stand out.

The graph never picks a winner between conflicting facts — a certified policy
does (see [conflict-adjudication](../conflict-adjudication)).

This skill is the final block of the EDW → Pets → Quine sync workflow: it takes the
records from [edw-fetch](../edw-fetch) and [pets-and-dependents](../pets-and-dependents)
and lands them in the living graph.

## For the recurring new-arrivals sync
Call the **`quine_ingest_batch`** tool (on the muster-quine connector) with the
**`batch_id`** — it ingests the whole staged batch (personnel + households + pets)
as event-sourced facts. Report the batch_id and how many records were ingested.
