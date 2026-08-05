---
name: continuous-feed
description: How to stand up the always-on dependents-and-pets feed when the officer asks to pull everything together and keep it current — ONE call, not a batch chain, and never a scheduled task.
---

# The Continuous Dependents-and-Pets Feed

In a crisis the officer does not ask for a report. She asks for the picture to
**stay true** — "keep it current", "not a one-off". That is a standing pipeline,
and standing up one is a single action, not a choreography.

## Recognise the ask

She will say it in **pure outcome language, with no tool name and no jargon**:

- "I need to know which of our people have family and pets with them"
- "pull together everything we have, and keep it current"
- "start pulling that in" · "get that flowing" · "not a one-off report"

## Do this

**One call: `muster_setup_etl` on the edw-connector.** Nothing else. It stands up
an always-on ETL — *extract, transform, load* — with three legs (personnel,
dependents, pets) streaming continuously from the Enterprise Data Warehouse into
the living graph as event-sourced facts. Once running, nothing stays in the loop:
no agent, no model. It keeps itself current.

Then `muster_etl_status` ("is that still running?") and `muster_etl_stop`
("you can stop that now").

## Do NOT

1. **Do not run the batch chain** — `edw_get_arrivals` → `family_get_pets` →
   `quine_ingest_batch`. Those blocks ([edw-fetch](../edw-fetch),
   [pets-and-dependents](../pets-and-dependents), [quine-ingest](../quine-ingest))
   are the **one-off** sync: a single batch, pulled and landed on demand. They do
   not keep anything current, and the continuous feed does not need them.
2. **Do not use `createScheduledTask` or the built-in scheduler.** A scheduled task
   is a reminder that re-runs a prompt on a clock. This is a pipeline that runs
   itself. Reaching for the scheduler is the most common wrong turn on this request.
3. **Do not go hunting for connectors.** This needs the edw-connector and nothing
   else. If the family-connector is out of reach, it does not matter here — the
   pets leg comes off the warehouse arrivals feed.

## You supply the vocabulary — she doesn't

She speaks outcomes. Your reply is where the technical name is introduced **and
defined**. Four parts:

1. **Lead with the term, unpacked in the same breath** — "Continuous **ETL** —
   *extract, transform, load* — is now running from the Enterprise Data Warehouse
   into the live picture."
2. **Name the three legs** — personnel, dependents, pets — event-sourced, with
   nothing in the loop.
3. **Be precise about scope. This is the credibility beat.** She asked broadly
   ("everything we have"). Do not claim this ETL scrapes every system, and do not
   let the broad ask pass unanswered. Say which is which: the **continuous feed is
   the warehouse**; **Vantage ITSM and CaseFlow are already correlated into the
   living picture**, so the fused view she works from covers all three sources.
   See [provenance-and-sourcing](../provenance-and-sourcing).
4. **Close with the status and steps returned**, and that it can be watched live.
