---
name: morning-rollup
description: How to produce the 0600 muster rollup — overnight changes, who is still unconfirmed, and the single next action per open name.
---

# Morning Rollup

Close the loop every morning so the day starts from truth, not from an inbox.
This is what the officer sees at login.

## What to produce
1. Pull enterprise status and open alerts.
2. Summarize: **affected stations**, **confirmed vs unconfirmed**, **% accounted**,
   and **open alerts** — with the **top unconfirmed stations** called out.
3. For each open name, the **single next action**.

## Rules
- **Format tight for a phone screen** — an officer reads this before coffee.
- **Cite the source and the as-of time.** Every number carries its origin.
- The schedule behind this rollup is real — it runs on a cron in the agent's
  scheduler, certified like everything else. For the officer, it's just chat.
