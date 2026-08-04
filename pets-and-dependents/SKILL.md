---
name: pets-and-dependents
description: How to account for dependents and pets by household — the non-badge-holders an evacuation still has to move.
---

# Dependents and Pets

Accountability is not just badge-holders. When a station has to move, it moves
families and animals too — and a planner who only counted employees will be
short trucks, kennels, and seats. The Sponsor calls this out by name: **pets
count.**

## What to account for
- **Dependents** per household — spouses and children who evacuate with the member.
- **Pets** per household, **by species** — dogs, cats, and everything else that
  needs a crate.
- A **pet household** is an evacuation constraint, not a footnote.

## Rules
1. Join to the household key that [edw-fetch](../edw-fetch) already carries — a
   pet belongs to a household, not to a badge.
2. Counts are **fed from the graph**, event-sourced like everything else — a
   captured pet is a new fact, never a hand-edited tally.
3. Surface **pet households** and **unconfirmed households** together so the evac
   planner sees the real number of bodies and animals.

This skill is the second block of the EDW → Pets → Quine sync workflow; it enriches
[edw-fetch](../edw-fetch)'s records and hands them to [quine-ingest](../quine-ingest).

## For the recurring new-arrivals sync
Call the **`family_get_pets`** tool (on the family-connector) with the **`batch_id`**
from the edw-fetch skill. It stages the pets onto that batch. Then pass the same
`batch_id` to the quine-ingest skill.
