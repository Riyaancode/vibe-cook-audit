# project-audit — Claude Code plugin

A stack-aware codebase audit that ends in three client-ready documents:

| File | Audience | Contents |
|---|---|---|
| `audit.md` | Developers | Full technical audit with file references, per-stack deep dives, raw tooling output |
| `audit-brief.md` | Clients / non-technical readers | Plain-language verdict, scorecard, blockers, phased plan, questions for the client |
| `estimate.md` | Clients | Hours per task with a one-line "what is included", phase subtotals, timeline |

## Install

```
/plugin marketplace add Riyaancode/claude-audit-plugin
/plugin install project-audit@Riyaancode
```

## Commands

| Command | What it does |
|---|---|
| `/audit` | Runs the full audit on the current project. Writes `audit.md` and `audit-brief.md`. |
| `/audit-brief` | Regenerates `audit-brief.md` from an existing `audit.md`. |
| `/estimate [phase1=8 phase2=40 ...]` | Writes `estimate.md` from `audit-brief.md`. Optional phase hour budgets rescale that phase's rows to match exactly. |

## What `/audit` does

1. **Detects the stack** from `package.json`, other manifests and folder names. Builds a "What this project is built with" table (major modules only).
2. **Pre-flight plugin check.** For every detected service with an official Claude plugin (Stripe, Supabase, Convex, Firebase, Expo, RevenueCat, Twilio, AWS, Clerk, Neon, …) it checks whether that plugin is installed. If any are missing it prints the install commands and **waits** for you to install them or say "continue". Specialist reviewers load those plugins' best-practice skills, so the audit is more accurate with them.
3. **Baseline checks.** Install, typecheck, lint, test, build, dependency vulnerability audit, and a secret scan of the built bundle. Results go verbatim into the appendix.
4. **Parallel reviewer agents**, all read-only: structure, security, frontend/features, dependencies, plus specialists for each detected service.
5. **Verification pass.** Every Critical and High finding is re-checked directly against the code before it is published. Findings that do not reproduce are dropped.
6. **Writes the documents** from templates.

The audited project is never modified except for the three output files.

## Tuning

- `skills/audit-workflow/references/service-registry.md` — add or update services, plugin ids and install commands.
- `skills/estimate-writing/references/calibration.md` — hidden multipliers applied to raw hour estimates. Tune as you learn how long tasks really take.
- `skills/audit-workflow/checklists/*.md` — what each reviewer looks for.

## Requirements

Claude Code with the Agent tool available. No API keys. Works with npm, pnpm, yarn or bun projects; other ecosystems get a structure/security review without baseline tooling.

## License

MIT
