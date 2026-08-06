---
name: briefing-deck
description: How to export a muster answer as a one-slide PowerPoint briefing — the look, the markings, and the sources that must survive the trip out of the system.
---

# Briefing Deck

When the officer asks for a slide, a deck, a briefing, or "something I can send" for a
station, call **`muster_officer_slide({ station })`**. It returns a real one-slide `.pptx`
as a download link, rendered from the graph in about ten seconds.

**Do not author the deck yourself.** Authoring it took twenty tool calls and three
minutes, produced a different slide every run, and could die mid-build on an upstream
error. Everything on the page is already computed; the layout is now code that is tested
for collisions and for the honesty rules below, so it comes out the same every time.

Say the sheet is ready, name the file, and give the officer the headline the tool returns:
how many are unaccounted for, how many are unreachable, how many conflicts await a ruling.
For the unreachable part, **place `unreachable.sentence` as written** — never say "N unreachable"
on your own. It goes out stamped DRAFT — say plainly that a person must review it before release.

Ask for `format: "html"` only if the officer explicitly wants a printable sheet instead of
a deck. Never use `muster_call_tree` for this — it joins through pets, so it returns only
people who own one and drops most of the roster from an emergency call tree.

## What the slide contains, and why

Read `muster_officer_brief({ station })` if you need to discuss the numbers in chat — the
slide is rendered from exactly this payload. The rules below are already enforced by the
renderer; they are written down so you can explain the page and spot it if it ever drifts.

### Two scopes. Never put them in one sentence.

The payload has **`scope_station`** (the whole station) and **`scope_roster`** (the staff
officers, a subset). Each carries a finished `label` — use those strings.

A deck once printed *"21 staff officers · 14 households · 21 children · 4 pets · station
total 127"*. Every number is true and the line is a lie: the households, children and pets
are the **officers'**, while 127 is the **station's** — and the station has roughly six
times the officers' figure. A station reading that line plans for a fraction of the
children it actually has.

(No literal counts here on purpose. The arrivals feed moves KESTREL between reads, and
every earlier attempt to pin this in prose went stale — this passage itself claimed "91
households and 130 children" against a truth of 87 and 126.)

If the slide shows dependents beside a station total, it shows
`scope_station.label`. Roster figures always say whose they are.

### The sheet covers the whole station unless you narrow it

`position_type` holds five **peer** job families — officer, specialist, support, attache, analyst — not a
seniority ladder with "officer" on top of the rest. One family is roughly a sixth of a station, so
`muster_officer_slide({ station })` rosters **all** of them by default.

Pass `role` only when the officer names a family ("just my officers"), and say that you narrowed it. A
commander who asks who is unaccounted for and receives a sheet covering 17% of the station, with nothing on
the page saying so, has been quietly misinformed — that is the same failure as mixing the two scopes.

`scope_roster.excluded_note`, when present, names who is on **no** roster: people carrying no
`position_type` at all. Today that is the arrivals feed — in transit, no phone, no email. Say it. The
least reachable people on the station are the ones no roster can list.

### The same rule for reachability

`unreachable` carries **two** populations: `unreachable.roster` (staff officers with no
contact path) and `unreachable.station` (station-wide conflicts flagged unreachable —
arrivals, duplicates, people not on this roster at all). They are different numbers about
different people.

Place **`unreachable.sentence`**, which names both. Do not pick one and call it
"unreachable". A deck printed `1 unreachable` from the station-wide count while the chat
answer beside it said `0 officers unreachable`; both were true, and the reader had no way
to tell which question either was answering.

### The personnel total has a caveat — say it

`scope_station.personnel` is what **EDW rosters**, and EDW rosters a member in PCS at their
*origin* station. So people assigned to this station can be missing from its own count —
at KESTREL, eight of them, four being officers.

When `roster_gap.label` is present, say it. It is hedged ("at least N") on purpose: the
derivation can only see members who have a household based here, so anyone rostered away
with no household record is invisible to it. Do not tighten that hedge into an exact count.

### Copy the strings; do not compose your own

The payload writes out anything a reader acts on. Place these verbatim:

- `officers[].action` — the call instruction. **Do not derive it from `risk`.** A deck
  turned `no_second_adult` into "ESCALATE via lead" for officers who had their own phone;
  you would call those people directly. `action` already says which.
- `scope_roster.gap_label`, `scope_roster.second_adult_label` — these count **officers**,
  not households. A deck mislabelled the unit.
- `scope_roster.household_note` — states all three buckets (with dependents / no household
  record / household on file but no dependents listed). Do not do the subtraction yourself;
  the three do not always sum the way two of them suggest.
- `scope_station.headline` — the unaccounted sentence.
- `unreachable.sentence` — reachability, with both populations named.
- `roster_gap.label` — the caveat on the personnel total, when present.

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
   unaccounted for (`scope_station.unconfirmed`), in critical red, with `of N personnel`
   beneath it. A percentage may sit beside it as support. A watch officer counts people;
   percentages are for reports. Every hero-row tile names its population — `PERSONNEL ·
   STATION`, `STAFF OFFICERS`, `CHILDREN · STATION` — because two of those figures come
   from `scope_station` and one from `scope_roster`, and a bare label cannot tell you which.
4. **First calls, in the order the payload gives them.** The roster arrives ranked by
   consequence — no contact path first, then most dependents. Do not re-sort
   alphabetically. One block per person: name bold in `#10161F`, role and muster status
   beside it, phone in a monospaced face and large enough to dial from across a room.
   Household — spouse and number, children, pets — indented beneath the person.
   Whoever has no number stays on the list; their action is `ESCALATE — via station
   lead` with the lead's number. Dropping someone because they are unreachable is how
   people get missed.
5. **What we do not have.** State `scope_roster.no_contact_path.length` plainly, with the
   names, under a label that says these are staff officers. The conflicts block below is
   station-wide, so a bare "0 unreachable" beside an unreachable name in that block reads
   as a contradiction — `unreachable.station_label` is the sentence that resolves it, and
   the page carries it whenever the two populations disagree.
6. **Conflicting information — officer decision required.** The disagreements the system
   found, highest severity first, each showing both sides with its source and value
   (`MUSTER GRAPH: KESTREL` ≠ `EDW: IN_TRANSIT`). Print `conflict_count` as the badge —
   the true total — and say how many are shown. Never truncate silently to a tidy
   number. State that no source was auto-selected and that the ruling is the officer's.
7. **Chain of command** in its own short block, labelled `(derived)`.
8. **Sources footer**, under a hairline `#D6DEE8`: one line per system — what it is,
   what it covers, and when it was observed — plus one line for what was derived.

## Colors

These are the renderer's actual values — quoted so you can describe the page, not so you
can repaint it. Status color only ever means muster state: confirmed `#1E7A3C`,
unconfirmed `#B07818`, critical `#A33030`. Source color is fixed: EDW `#1C5CAB`, MUSTER
GRAPH `#14707F`, PETNET `#5B50B8`, derived `#74849B`. The classification banner is
`#007A33`. Amentum green is brand only — it never encodes data.
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
- **The file is named for you.** `muster_officer_slide` returns the name — currently
  `<station>-accountability-<YYYY-MM-DD>.pptx`, lowercase. Use what the tool returns; do
  not construct a name or rename the download.

## How the renderer holds the page together

Not instructions for you — you are not building this. Written down so you can explain the
page, and notice if it ever drifts.

- **Rows advance a cursor; nothing is hand-placed at an absolute y.** That is what makes
  overlap impossible by construction rather than something discovered in a render. One
  earlier hand-placed build chased 29 overlap warnings through six patch cycles, each fix
  moving something else.
- **The canvas is fixed** — 13.333 × 7.5in for the deck, 1920 × 1080 for the HTML sheet —
  so vertical budget is real. When a new row has to appear it either fits in existing
  slack or something else gives; the right column shows fewer conflict cards and the badge
  still states the true total, so nothing is hidden by shrinking.
- **Arial by name**, resolved in the render container to metric-compatible Liberation
  Sans. What is measured at build time is what PowerPoint lays out.
- **Geometry and scope are tested, not eyeballed** — `npm run verify:scope` in the
  supernatural repo renders both artifacts from fixtures and asserts that every figure
  sits under a label naming its own population, and that no two text boxes collide. If you
  are told the page changed, that is what proves it still holds.

## Why the footer is not optional

A call tree with no sources is a list of phone numbers someone has to take on faith.
The footer is what makes it accountability. See
[provenance-and-sourcing](../provenance-and-sourcing) for the principle underneath,
and [muster-accountability](../muster-accountability) for what the numbers mean.
