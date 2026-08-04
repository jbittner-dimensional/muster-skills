# muster-skills

Git-backed **Agent Skills** source for the muster demo, synced into Obot as a
Skill Source (av-script-v3 Platform segment, 0:50–2:45). Each skill is a reusable
**building block** — English tradecraft the agent follows, versioned in source
control and gated by access policy.

Obot requires a **GitHub (HTTPS)** repository — it fetches skills via the GitHub
API — so push this repo to `https://github.com/<owner>/muster-skills` and register
it with `supernatural/scripts/register-skill-source.mjs`. Each skill is a
**subdirectory** with a `SKILL.md` (YAML frontmatter + English body); a `SKILL.md`
at the repo root is rejected.

## The library

**Data-plane blocks** (compose into the every-5-minutes EDW → Pets → Quine sync workflow)
- `edw-fetch` — pull personnel + households from the warehouse connector.
- `pets-and-dependents` — account for dependents and pets by household (the Sponsor calls out pets).
- `quine-ingest` *(Muster-Quine)* — ingest records as event-sourced facts into the living graph.

**Mission tradecraft blocks**
- `muster-accountability` — the overall personnel-accountability operating procedure.
- `conflict-adjudication` — surface disagreements → adjudicate with rationale → codify policy.
- `emergency-recall` — 7700 → draft recall → human sends.
- `evac-planning` — compose the evacuation picture (households, pets by species, unconfirmed).
- `morning-rollup` — the 0600 muster rollup for the officer at login.
- `provenance-and-sourcing` — the first principle: every value carries its origin and age; the graph never picks a winner.
