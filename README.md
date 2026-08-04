# muster-skills

Git-backed **Agent Skills** source for the muster demo, synced into Obot as a
Skill Source (av-script-v3 Platform segment, 0:50–2:45).

Obot requires a **GitHub (HTTPS)** repository — it fetches skills via the GitHub
API — so push this repo to `https://github.com/<owner>/muster-skills` and
register it with `supernatural/scripts/register-skill-source.mjs`.

Each skill is a **subdirectory** with a `SKILL.md` (YAML frontmatter + English
body). Obot discovers skills one directory deep; a `SKILL.md` at the repo root
is rejected.

- `muster-accountability/SKILL.md` — the personnel-accountability operating
  procedure (the "unit of tradecraft" shown in the SKILL.md shot).
