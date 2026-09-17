# project-audit plugin — design

Date: 2026-09-18
Status: approved in conversation, implementation to follow

## Purpose

Package the audit-to-estimate workflow used on the Skill IQ project into a
reusable Claude Code plugin that anyone can install with two commands and run
on any codebase. It produces three client-ready documents in the audited
project's root: a full technical audit, a plain-language brief, and an hours
estimate.

## Distribution

- Repo: `github.com/Riyaancode/claude-audit-plugin` (public), acting as its own
  marketplace via `.claude-plugin/marketplace.json`.
- Plugin name: `project-audit`.
- Install: `/plugin marketplace add Riyaancode/claude-audit-plugin` then
  `/plugin install project-audit@Riyaancode`.
- Local dev path: `~/personal-projects/claude-audit-plugin`.

## User-facing surface

| Command | Input | Output |
|---|---|---|
| `/audit` | current project | `audit.md`, `audit-brief.md` |
| `/audit-brief` | existing `audit.md` | `audit-brief.md` |
| `/estimate [phaseN=hours ...]` | existing `audit-brief.md` | `estimate.md` |

All three commands are thin: they load the matching skill and pass arguments.

## Workflow of `/audit`

1. **Stack detection.** Read `package.json` (and `requirements.txt`, `Podfile`,
   `pubspec.yaml`, `go.mod` when present) plus top-level folder names
   (`supabase/`, `convex/`, `prisma/`, `capacitor.config.*`, `app.json` for
   Expo). Match against `references/service-registry.md`. Produce two things:
   the **stack table** (module → provider, major services only) and the list of
   **specialist reviewers** to activate.
2. **Pre-flight plugin check.** For every detected service that has an entry in
   the registry, check whether its official plugin is installed (look under
   `~/.claude/plugins/cache/` and `~/.claude/plugins/marketplaces/`). Print the
   exact install commands for missing ones, explain that specialist reviewers
   load those plugins' best-practice skills, and **stop for the user** to
   install or reply "continue". Never proceed silently.
3. **Baseline checks.** Detect package manager from lockfile. Install
   dependencies, run typecheck, lint, unit tests, production build, dependency
   vulnerability audit. Grep the built client bundle for secret-looking env
   names. Record raw results verbatim for the appendix. Any step that cannot
   run is reported as "could not run", never skipped silently.
4. **Fan-out review.** Launch reviewer agents in parallel, all read-only:
   - Always: `structure-reviewer`, `security-reviewer`, `frontend-reviewer`,
     `dependency-reviewer`.
   - By detection: `stripe-reviewer`, `supabase-reviewer`, `convex-reviewer`,
     `firebase-reviewer`, `sql-orm-reviewer` (Prisma/Drizzle/Neon/Postgres),
     `mongodb-reviewer`, `auth-provider-reviewer` (Clerk/Auth0/NextAuth),
     `mobile-reviewer` (Capacitor/Expo/React Native), `messaging-reviewer`
     (Twilio/SendGrid/Resend), `cloud-reviewer` (AWS/GCP).
   Each agent gets a fixed brief: scope, the checklist file to follow, the
   instruction to load the relevant official plugin skill first if installed,
   the required report shape (executive summary, findings table with
   file:line and severity, inventory, what's good), and "say could not verify
   rather than guess".
5. **Verification pass.** Before writing, the orchestrator re-checks every
   Critical and High finding directly against the code (grep/read). Findings
   that do not reproduce are dropped or downgraded. This step is mandatory and
   recorded in the audit's method line.
6. **Write `audit.md`** from `templates/audit.md`.
7. **Write `audit-brief.md`** by loading `audit-brief-writing` and applying it
   to `audit.md`.
8. **Report** in chat: verdict, blockers, where the files are, what could not
   be verified.

## Skills

### `audit-workflow`
The orchestration above. Includes: detection rules, pre-flight behaviour,
baseline command matrix per package manager, agent briefs, verification rule,
severity definitions (Critical / High / Medium / Low with one-line criteria),
and the output template.

### `audit-brief-writing`
Rules for the client version: no file paths, no code identifiers, no line
numbers; effort as Low / Medium / High only, never time; fixed section order
(short answer table, scorecard, what works, blockers, important problems by
group, feature status table, phased plan, could-not-verify, questions for the
client); glossary only when a technical word is unavoidable; target length
under ~160 lines.

### `estimate-writing`
Rules for `estimate.md`: one row per task from the brief's phased plan with
columns `#`, `Task`, `What is included` (one plain sentence), `Hours`; phase
subtotal and working-day line; totals table; buffer line; "Not included"
section. Hours are produced by (a) a raw estimate from
`references/effort-bands.md`, then (b) multiplied by a hidden per-task-type
calibration factor from `references/calibration.md`, then (c) rounded to 0.5.
If the user passes `phaseN=hours`, rows in that phase are rescaled
proportionally so the subtotal matches exactly. Working days assume 6
productive hours/day unless overridden. Mobile app work is estimated only
when the user asks.

## Agents

One markdown file per reviewer under `agents/`, each with a `name`,
`description`, read-only tool list, and a body that points at its checklist
under `skills/audit-workflow/checklists/<name>.md`. Checklists are derived
from the Skill IQ audit briefs and the official plugin skills' rules (Stripe:
webhook signatures, no `payment_method_types`, Accounts v2, idempotency,
fulfilment via webhooks; Supabase: RLS on every exposed table, no
`user_metadata` in policies, `security_invoker` views, `SECURITY DEFINER`
guards and `search_path`, storage policies, migrations replayable from
scratch).

## References shipped with the plugin

- `service-registry.md` — dependency pattern → service → module category →
  plugin id → install commands → reviewer to activate.
- `effort-bands.md` — raw hour bands by task type.
- `calibration.md` — hidden multipliers by task type (contained fix 0.35,
  integration/payment 0.60, broad cleanup 0.40, new feature 0.50, docs/config
  0.35) with a note on how they were derived and how to tune them.
- `severity.md` — definitions.
- `templates/audit.md`, `templates/audit-brief.md`, `templates/estimate.md`.

## Constraints

- Read-only against the audited project except for the three output files.
- No network calls beyond what the package manager and official plugins do.
- No API keys required.
- Works with npm, pnpm, yarn, bun; falls back gracefully when a tool is absent.

## Out of scope for v1

xlsx output, CI integration, web dashboard, auto-fixing findings.

## Testing

Smoke test: install the plugin from the local path, run `/audit` on the Skill
IQ project, confirm the three files are produced, the stack table matches
reality, the pre-flight lists Stripe/Supabase plugins as installed, and the
Critical findings match the manual audit. Run `/estimate phase2=40` and
confirm the phase subtotal is exactly 40.
