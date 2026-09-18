# vibe-cook-audit — Claude Code plugin

Taste it, cook it, plate it, bill it. A stack-aware codebase audit kitchen that ends in client-ready documents.

| Command | What it does | Output |
|---|---|---|
| `/taste` | Quick scan: stack table, build, typecheck, tests, vulnerability check, hygiene, one-page first impression. Minutes, no agents. | `taste.md` |
| `/cook` | Full audit: detects the stack, recommends official plugins, runs baseline checks, fans out parallel reviewer agents, verifies every Critical/High finding by hand. | `audit.md`, `audit-brief.md` |
| `/plate` | Rewrites an existing `audit.md` into the plain-language client brief. | `audit-brief.md` |
| `/bill [phase2=40 …] [include-mobile]` | Hours and timeline per fix, for client quotes. Optional phase budgets rescale that phase exactly. | `estimate.md` |

## Install

```
/plugin marketplace add Riyaancode/vibe-cook-audit
/plugin install vibe-cook-audit@Riyaancode
```

## What `/cook` does

1. **Detects the stack** from manifests, folder signals and env-var/hostname greps. Builds a "What this project is built with" table (major modules only).
2. **Pre-flight plugin check.** For every detected service with an official Claude plugin (Stripe, Supabase, Convex, Firebase, Expo, RevenueCat, Twilio, AWS, Clerk, Neon, …) it checks whether that plugin is installed. If any are missing it prints the install commands and **waits** for you to install them or say "continue". Specialist reviewers load those plugins' best-practice skills, so the audit is more accurate with them.
3. **Baseline checks.** Install, typecheck, lint, test, build, dependency vulnerability audit, secret scan of the built bundle. Results go verbatim into the appendix.
4. **Parallel reviewer agents**, all read-only: structure, security, frontend/features, dependencies, plus specialists for each detected service (14 agents in total).
5. **Verification pass.** Every Critical and High finding is re-checked directly against the code before it is published. Findings that do not reproduce are dropped.
6. **Writes the documents** from templates.

The audited project is never modified except for the output files.

## Tuning

- `skills/audit-workflow/references/service-registry.md` — services, plugin ids, install commands, detection signals.
- `skills/estimate-writing/references/calibration.md` — hidden multipliers applied to raw hour estimates. Tune as you learn how long tasks really take.
- `skills/audit-workflow/checklists/*.md` — what each reviewer looks for.

## Requirements

Claude Code with the Agent tool available. No API keys. Works with npm, pnpm, yarn or bun projects; other ecosystems get a structure/security review without baseline tooling.

## License

MIT
