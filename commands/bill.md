---
description: Estimate hours and timeline for the fixes in audit-brief.md, for client quotes; optional phase or total budgets and phase exclusions
argument-hint: [phaseN=<hours>] [total=<hours>] [exclude=pN]
---

Load the `vibe-cook-audit:estimate-writing` skill with the Skill tool and follow it.

Arguments from the user: $ARGUMENTS

Parse `phaseN=<hours>` / `pN=<hours>` and `total=<hours>` as exact budgets to rescale to. Parse `exclude=phaseN` / `exclude=pN` (repeatable or comma-separated) as phases to leave out of the estimate. Find the brief per the skill's Input rules (default `audit-brief.md` in the current working directory); if none exists, say so and suggest `/cook` first. Write `estimate.md` next to it.
