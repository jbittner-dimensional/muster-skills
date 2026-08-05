---
name: briefing-deck
description: How to export a muster answer as a one-slide PowerPoint briefing — the look, the markings, and the sources that must survive the trip out of the system.
---

# Briefing Deck

When the officer asks for a slide, a deck, a briefing, or "something I can send",
build a real `.pptx` with the `slides` skill. What leaves this system is a work
product someone forwards to a general. It carries its markings and its sources, or
it does not go out.

## What to produce

**One slide. 16:9. Everything on it.** Never a title slide and never a page to click
through — the officer asked for the answer, not a deck.

1. **Classification bar, top AND bottom**, full width, white on green `#007A33`:
   `UNCLASSIFIED // DEMONSTRATION // SYNTHETIC DATA`. Every slide, no exceptions.
2. **Header band** — navy `#10161F`, full width, directly under the top marking.
   Subject in white (`KESTREL · CALL TREE`), and beneath it in `#B7C4D4` the scope
   and the counts (`dog households · 2 to call · 0 unreachable`). Put the Amentum
   lockup right-aligned inside the band — `assets/amentum-logo-white.png` if it is
   there, otherwise the word `AMENTUM` in white, letter-spaced.
3. **Body, on white.** The numbered call order, one block per person: name bold in
   `#10161F`, role and status beside it, phone and email in a monospaced face so
   numbers align. Household — spouse, children, pets — indented beneath the person
   they belong to.
4. **Chain of command** in its own short block, labelled `(derived)`.
5. **Sources footer**, under a hairline `#D6DEE8`: one line per system — what it is,
   what it covers, and when it was observed — plus one line for what was derived.

## Colors

Status color only ever means muster state: confirmed `#3FA46A`, unconfirmed
`#E8A33D`, critical `#E05C5C`. Source color is fixed: EDW `#3987E5`, derived
`#9085E9`. Amentum green `#3EB55A` is brand only — it never encodes data.

## Rules

- **Pull the data in one call** (`muster_call_tree` for a call tree) and slide what it
  returns. Do not re-derive numbers, and do not call a tool per person.
- **The as-of is the stalest source, not the moment you built the slide.** If a system
  returned no timestamp, print `no observed-at returned` next to it. Never let the
  freshest stamp speak for all of them.
- **Never invent a contact, a timestamp, a name, or a person.** An empty field is
  printed as empty. A slide that guesses is worse than no slide.
- **Say what is inferred.** Chain of command is derived, not a system of record, and
  the slide says so where a reader will see it.
- **Set the theme font to Arial.** Do not specify Inter or any bundled font — it will
  substitute on the reviewer's machine and the deck will arrive looking broken. Run
  `detect_font.py` if you are unsure.
- **Render before delivering.** `render_slides.py`, look at the PNG, then
  `slides_test.py` for overflow. A slide that overflows its canvas is not delivered —
  reduce the roster or shrink the block, and render again.
- Deliver the `.pptx` and the authoring `.js`. Name the file
  `muster-call-tree-<STATION>-<YYYYMMDD>.pptx`.

## Why the footer is not optional

A call tree with no sources is a list of phone numbers someone has to take on faith.
The footer is what makes it accountability. See
[provenance-and-sourcing](../provenance-and-sourcing) for the principle underneath,
and [muster-accountability](../muster-accountability) for what the numbers mean.
