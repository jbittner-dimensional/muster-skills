---
name: briefing-deck
description: How to export a muster answer as a one-slide PowerPoint briefing — the look, the markings, and the sources that must survive the trip out of the system.
---

# Briefing Deck

When the officer asks for a slide, a deck, a briefing, or "something I can send",
build a real `.pptx` with the `slides` skill. What leaves this system is a work
product someone forwards to a general. It carries its markings and its sources, or
it does not go out.

## Where the numbers come from

**One call: `muster_officer_brief({ station })`.** It returns the station rollup, the
full roster for the role with contacts and dependents, per-row risk flags, the derived
chain of command, every source disagreement ranked by consequence, and the lineage with
each source's age. Slide what it returns.

Do not re-derive a number, do not call a tool per person, and do not use
`muster_call_tree` for this — it joins through pets, so it returns only people who own
one and silently drops most of the roster from an emergency call tree.

## What to produce

**One slide. 16:9. Everything on it.** Never a title slide and never a page to click
through — the officer asked for the answer, not a deck.

1. **Classification bar, top AND bottom**, full width, white on green `#007A33`:
   `UNCLASSIFIED // DEMONSTRATION // SYNTHETIC DATA`. Every slide, no exceptions.
2. **Header band** — navy `#10161F`, full width, directly under the top marking.
   Subject in white (`KESTREL · STAFF OFFICER ACCOUNTABILITY`), and beneath it in
   `#B7C4D4` the scope and counts. Right-aligned inside the band: `STATION AFFECTED`
   when the station is flagged, and **the event clock** — `T+HH:MM SINCE ALERT` from
   `event.elapsed_minutes`. Omit the clock entirely if `event` is null; never invent a
   start time. Put the Amentum lockup in the band —
   `assets/amentum-logo-white.png` if it is there, otherwise the word `AMENTUM` in
   white, letter-spaced.
3. **The count, not the ratio.** The largest thing on the slide is how many people are
   unaccounted for (`rollup.unconfirmed`), in critical red, with `of N personnel`
   beneath it. A percentage may sit beside it as support. A watch officer counts people;
   percentages are for reports.
4. **First calls, in the order the payload gives them.** The roster arrives ranked by
   consequence — no contact path first, then most dependents. Do not re-sort
   alphabetically. One block per person: name bold in `#10161F`, role and muster status
   beside it, phone in a monospaced face and large enough to dial from across a room.
   Household — spouse and number, children, pets — indented beneath the person.
   Whoever has no number stays on the list; their action is `ESCALATE — via station
   lead` with the lead's number. Dropping someone because they are unreachable is how
   people get missed.
5. **What we do not have.** State `rollup.no_contact_path.length` plainly, with the
   names. Name the scope — these are staff officers — because the conflicts block below
   is station-wide and a bare "0 unreachable" beside an unreachable name reads as a
   contradiction.
6. **Conflicting information — officer decision required.** The disagreements the system
   found, highest severity first, each showing both sides with its source and value
   (`MUSTER GRAPH: KESTREL` ≠ `EDW: IN_TRANSIT`). Print `conflict_count` as the badge —
   the true total — and say how many are shown. Never truncate silently to a tidy
   number. State that no source was auto-selected and that the ruling is the officer's.
7. **Chain of command** in its own short block, labelled `(derived)`.
8. **Sources footer**, under a hairline `#D6DEE8`: one line per system — what it is,
   what it covers, and when it was observed — plus one line for what was derived.

## Colors

Status color only ever means muster state: confirmed `#3FA46A`, unconfirmed
`#E8A33D`, critical `#E05C5C`. Source color is fixed: EDW `#3987E5`, derived
`#9085E9`. Amentum green `#3EB55A` is brand only — it never encodes data.
Status is never color alone: every status carries its word.

## Rules

- **The as-of is the stalest source, not the moment you built the slide.** If a system
  returned no timestamp, print `no observed-at returned` next to it. Never let the
  freshest stamp speak for all of them.
- **Never invent a contact, a timestamp, a name, or a person.** An empty field is
  printed as empty. A slide that guesses is worse than no slide.
- **Say what is inferred.** Chain of command and the designated next of kin are derived,
  not systems of record, and the slide says so where a reader will see it.
- **Nothing about how the slide was made.** No time-savings, no comparison to doing it
  by hand, no note about the system that produced it. This is a record a watch officer
  sends to a station; anything that reads like a sales document destroys the
  credibility of every number beside it. It carries its markings, its distribution line
  and its reference — nothing else.
- **Set the theme font to Arial.** Do not specify Inter or any bundled font — it will
  substitute on the reviewer's machine and the deck will arrive looking broken. Run
  `detect_font.py` if you are unsure.
- **Render before delivering.** `render_slides.py`, look at the PNG, then
  `slides_test.py` for overflow. A slide that overflows its canvas is not delivered.
  Overflow is not cosmetic: the first HTML build of this same content had the conflicts
  block drawing on top of the fourth call. Reduce the roster shown, shrink the block,
  and render again.
- Deliver the `.pptx` and the authoring `.js`. Name the file
  `muster-accountability-<STATION>-<YYYYMMDD>.pptx`.

## Why the footer is not optional

A call tree with no sources is a list of phone numbers someone has to take on faith.
The footer is what makes it accountability. See
[provenance-and-sourcing](../provenance-and-sourcing) for the principle underneath,
and [muster-accountability](../muster-accountability) for what the numbers mean.
