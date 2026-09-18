---
description: Estimate hours and timeline for the fixes in audit-brief.md, for client quotes; optional phase budgets like phase2=40
argument-hint: [phase1=8 phase2=40 total=78 include-mobile]
---

Load the `vibe-cook-audit:estimate-writing` skill with the Skill tool and follow it.

Arguments from the user: $ARGUMENTS

Parse `phaseN=<hours>` / `pN=<hours>` and `total=<hours>` as exact budgets to rescale to. Parse `include-mobile` as permission to estimate mobile-app tasks. Find the brief per the skill's Input rules (default `audit-brief.md` in the current working directory); if none exists, say so and suggest `/cook` first. Write `estimate.md` next to it.
