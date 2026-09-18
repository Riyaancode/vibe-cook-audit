---
description: Write estimate.md with hours per task from audit-brief.md; optional phase budgets like phase2=40
argument-hint: [phase1=8 phase2=40 total=78 include-mobile]
---

Load the `project-audit:estimate-writing` skill with the Skill tool and follow it.

Arguments from the user: $ARGUMENTS

Parse `phaseN=<hours>` / `pN=<hours>` and `total=<hours>` as exact budgets to rescale to. Parse `include-mobile` as permission to estimate mobile-app tasks. Read `audit-brief.md` from the current working directory; if it is missing, say so and suggest `/audit` first. Write `estimate.md` next to it.
