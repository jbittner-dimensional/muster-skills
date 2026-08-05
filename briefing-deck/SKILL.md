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

**One call: `muster_officer_brief({ station })`.** Slide what it returns.

Do not re-derive a number, do not call a tool per person, and do not use
`muster_call_tree` for this — it joins through pets, so it returns only people who own
one and silently drops most of the roster from an emergency call tree.

### Two scopes. Never put them in one sentence.

The payload has **`scope_station`** (the whole station) and **`scope_roster`** (the staff
officers, a subset). Each carries a finished `label` — use those strings.

A deck once printed *"21 staff officers · 14 households · 21 children · 4 pets · station
total 127"*. Every number is true and the line is a lie: the households, children and pets
are the **officers'**, while 127 is the **station's**. KESTREL has 91 households and 130
children. A station reading that line plans for 21 children instead of 130.

If the slide shows dependents beside a station total, it shows
`scope_station.label`. Roster figures always say whose they are.

### Copy the strings; do not compose your own

The payload writes out anything a reader acts on. Place these verbatim:

- `officers[].action` — the call instruction. **Do not derive it from `risk`.** A deck
  turned `no_second_adult` into "ESCALATE via lead" for officers who had their own phone;
  you would call those people directly. `action` already says which.
- `scope_roster.gap_label`, `scope_roster.second_adult_label` — these count **officers**,
  not households. A deck mislabelled the unit.
- `scope_station.headline` — the unaccounted sentence.

### Say when the chain itself is doubtful

Each `chain[]` entry has `disputed`. When true, print `DISPUTED — see conflicts` beside
that name. A deck sent the reader to the region lead for escalation while listing that
same person in a location conflict lower on the page.

### Never point at something you did not produce

A deck said the full roster was "in the accompanying export" when no export existed. If a
figure is not on the slide, either publish the file in the same turn and name it, or say
plainly that it is not included. Never reference an artifact you have not created.

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
- **Stack rows from a cursor; do not hand-place y-coordinates.** Keep one `y` variable per
  column and advance it by the row height after each row. Hand-placed absolute positions
  are why one build chased 29 overlap warnings through six patch cycles: every fix moved
  something else. With a cursor, a font or size change moves the whole column together.
- **Render before delivering.** `render_slides.py` (or `soffice --headless --convert-to
  pdf` then `pdftoppm`), look at the PNG, then `slides_test.py`. Read its warnings with
  judgement: it flags overlapping *bounding boxes*, and a label sitting inside its own
  coloured bar is intentional. Fix what is visible in the PNG; do not chase a warning
  count to zero.
- **Ignore `detect_font.py` reporting Arial and Courier New "missing".** The container
  resolves them to Liberation Sans and Liberation Mono, which are metric-compatible —
  same advance widths — so the layout you see in the render is the layout PowerPoint
  produces. Keep specifying Arial.
- Deliver the `.pptx` and the authoring `.js`. Name the file
  `muster-accountability-<STATION>-<YYYYMMDD>.pptx`.

## Why the footer is not optional

A call tree with no sources is a list of phone numbers someone has to take on faith.
The footer is what makes it accountability. See
[provenance-and-sourcing](../provenance-and-sourcing) for the principle underneath,
and [muster-accountability](../muster-accountability) for what the numbers mean.
