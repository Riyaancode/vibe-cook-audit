# project-audit Plugin Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Ship a Claude Code plugin `project-audit` that runs a stack-aware, multi-agent codebase audit and produces `audit.md`, `audit-brief.md` and `estimate.md`.

**Architecture:** Three slash commands load three skills. The `audit-workflow` skill orchestrates detection, pre-flight plugin check, baseline checks, parallel reviewer agents, a verification pass, and template-driven writing. Reviewer agents are markdown definitions that point at per-stack checklists. References hold the service registry, severity, effort bands and hidden calibration factors.

**Tech Stack:** Claude Code plugin format (`.claude-plugin/plugin.json`, `skills/`, `agents/`, `commands/`), markdown only, no runtime code.

**Spec:** `docs/superpowers/specs/2026-09-18-project-audit-plugin-design.md`

## Global Constraints

- Plugin name `project-audit`; marketplace owner `Riyaancode`; repo `github.com/Riyaancode/claude-audit-plugin`.
- Read-only against the audited project except `audit.md`, `audit-brief.md`, `estimate.md`.
- Brief: no file paths, no code identifiers, effort Low/Medium/High only, never time.
- Estimate: hidden calibration factors from `references/calibration.md`; `phaseN=hours` rescales proportionally; 6 productive hours/day.
- Pre-flight must stop and wait for the user when recommended plugins are missing.
- Every skill `description` starts with "Use when" and does not summarise workflow.

---

## File structure

```
claude-audit-plugin/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── README.md
├── LICENSE
├── commands/
│   ├── audit.md
│   ├── audit-brief.md
│   └── estimate.md
├── skills/
│   ├── audit-workflow/
│   │   ├── SKILL.md
│   │   ├── references/
│   │   │   ├── service-registry.md
│   │   │   ├── severity.md
│   │   │   ├── baseline-checks.md
│   │   │   └── agent-brief.md
│   │   ├── checklists/
│   │   │   ├── structure.md  security.md  frontend.md  dependencies.md
│   │   │   ├── stripe.md  supabase.md  convex.md  firebase.md  sql-orm.md
│   │   │   ├── mongodb.md  auth-provider.md  mobile.md  messaging.md  cloud.md
│   │   └── templates/
│   │       └── audit.md
│   ├── audit-brief-writing/
│   │   ├── SKILL.md
│   │   └── templates/audit-brief.md
│   └── estimate-writing/
│       ├── SKILL.md
│       ├── references/effort-bands.md
│       ├── references/calibration.md
│       └── templates/estimate.md
└── agents/
    ├── structure-reviewer.md  security-reviewer.md  frontend-reviewer.md  dependency-reviewer.md
    ├── stripe-reviewer.md  supabase-reviewer.md  convex-reviewer.md  firebase-reviewer.md
    ├── sql-orm-reviewer.md  mongodb-reviewer.md  auth-provider-reviewer.md
    ├── mobile-reviewer.md  messaging-reviewer.md  cloud-reviewer.md
```

---

### Task 1: Plugin scaffold

**Files:** Create `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`, `README.md`, `LICENSE` (MIT), `.gitignore`.

- [ ] Write `plugin.json` with name `project-audit`, version `0.1.0`, description, author `Riyaancode`, repository URL, keywords.
- [ ] Write `marketplace.json` with owner `Riyaancode`, one plugin entry `project-audit`, `source: "./"`.
- [ ] Write README with install commands, the three commands, what gets produced, and how to tune the registry and calibration.
- [ ] `git add -A && git commit -m "feat: plugin scaffold"`.

### Task 2: audit-workflow references

**Files:** Create `skills/audit-workflow/references/service-registry.md`, `severity.md`, `baseline-checks.md`, `agent-brief.md`.

- [ ] `service-registry.md`: table columns `Dependency patterns | Folder/file signals | Service | Module | Plugin id | Marketplace | Install commands | Reviewer`. Cover Stripe, Supabase, Neon, Prisma, Drizzle, MongoDB, Convex, Firebase, Clerk, Auth0, NextAuth, Expo, Capacitor, React Native, RevenueCat, Twilio, SendGrid, Resend, Postmark, AWS, GCP, Vercel, Cloudflare, Sentry, Upstash, Redis, S3, Cloudinary, Mapbox/Google Maps, OpenAI/Anthropic. Mark rows without a known plugin as `—`.
- [ ] `severity.md`: Critical / High / Medium / Low definitions (one sentence each) and rule that Critical/High must be re-verified before publishing.
- [ ] `baseline-checks.md`: command matrix per package manager (npm/pnpm/yarn/bun) for install, typecheck, lint, test, build, audit; secret-scan grep list; "could not run" reporting rule.
- [ ] `agent-brief.md`: the exact prompt shape given to every reviewer (scope, checklist path, plugin skill to load if installed, report shape, could-not-verify rule, read-only rule).
- [ ] Commit.

### Task 3: reviewer checklists

**Files:** Create 14 files under `skills/audit-workflow/checklists/`.

- [ ] Each checklist: 10–25 concrete checks phrased as questions with what evidence to cite. Stripe and Supabase checklists carry the rules from the official plugin skills used in the Skill IQ audit. Structure/security/frontend/dependencies carry the checks from the Skill IQ reviewer briefs.
- [ ] Commit.

### Task 4: audit template and audit-workflow SKILL.md

**Files:** Create `skills/audit-workflow/templates/audit.md`, `skills/audit-workflow/SKILL.md`.

- [ ] Template: title/date/scope/method, bottom line table, stack table, scorecard, what works, blockers with effort, high-priority by group, per-stack deep dives (conditional), app security, feature matrix, code quality, operations/env inventory, launch plan, glossary, technical appendix per reviewer, raw tooling results.
- [ ] SKILL.md (< 600 words): frontmatter; overview; the 8-step workflow as a numbered recipe with file references; hard rules (stop at pre-flight, verify Critical/High, read-only, could-not-verify); red flags table.
- [ ] Commit.

### Task 5: reviewer agents

**Files:** Create 14 files under `agents/`.

- [ ] Each: frontmatter `name`, `description` ("Use when auditing … Read-only."), `tools: Read, Grep, Glob, Bash`; body: role, load `${CLAUDE_PLUGIN_ROOT}/skills/audit-workflow/checklists/<x>.md`, load named official plugin skill if installed, follow `references/agent-brief.md` report shape.
- [ ] Commit.

### Task 6: audit-brief-writing skill

**Files:** Create `skills/audit-brief-writing/SKILL.md`, `templates/audit-brief.md`.

- [ ] Template with fixed section order from the spec; stack table included.
- [ ] SKILL.md: positive recipe (what the brief IS), forbidden-content table with replacements (path → "the booking code", `RLS` → "database access rules", hours → Low/Medium/High), length target, tone examples taken from the Skill IQ brief.
- [ ] Commit.

### Task 7: estimate-writing skill

**Files:** Create `skills/estimate-writing/SKILL.md`, `references/effort-bands.md`, `references/calibration.md`, `templates/estimate.md`.

- [ ] `effort-bands.md`: raw hour bands by task type (contained fix, integration/payment, broad cleanup, new feature, docs/config, testing pass).
- [ ] `calibration.md`: factors 0.35 / 0.60 / 0.40 / 0.50 / 0.35 / 0.50, derivation table from Skill IQ (22→8, 67→40, 77→30), tuning instructions, rule that factors never appear in output.
- [ ] Template: header with basis/assumptions, per-phase 4-column table with subtotal and timeline line, totals table, buffer line, not-included list.
- [ ] SKILL.md: recipe (read brief → list tasks per phase → raw hours → apply factor → round 0.5 → apply `phaseN=` overrides by proportional rescale with exact sum → days = hours/6 → write). Include the rescale algorithm and a worked example.
- [ ] Commit.

### Task 8: commands

**Files:** Create `commands/audit.md`, `commands/audit-brief.md`, `commands/estimate.md`.

- [ ] Each: frontmatter `description`, `argument-hint`; body loads the skill via Skill tool and passes `$ARGUMENTS`.
- [ ] Commit.

### Task 9: validation and tests

- [ ] Run `claude plugin validate .` (if the CLI supports it) and fix errors.
- [ ] Install locally: `claude plugin install ~/personal-projects/claude-audit-plugin` or `/plugin install <path>`; confirm the three commands and 14 agents are listed.
- [ ] Test A (detection + pre-flight): dispatch a subagent with the `audit-workflow` skill against the Skill IQ project, instructed to stop after step 2. Expect: stack table with Frontend/Backend/Database/Auth/Payments/Storage/Email/SMS/Push/Mobile/Hosting rows; Stripe and Supabase plugins reported as installed; Twilio/Firebase/Capacitor plugin recommendations; a pause message.
- [ ] Test B (estimate rescale): dispatch a subagent with `estimate-writing` and the Skill IQ `audit-v2.md`, args `phase2=40`. Expect: phase 2 subtotal exactly 40, hours rounded to 0.5, no calibration factors mentioned in output.
- [ ] Test C (brief rules): dispatch a subagent with `audit-brief-writing` and the Skill IQ `audit.md`. Expect: no `/` paths, no backticks, no "hours/days/weeks", under 170 lines.
- [ ] Fix skills based on failures; re-run the failing test; commit.

### Task 10: publish

- [ ] Ask user to create empty public repo `Riyaancode/claude-audit-plugin`.
- [ ] `git remote add origin … && git push -u origin main`.
- [ ] Verify `/plugin marketplace add Riyaancode/claude-audit-plugin` + `/plugin install project-audit@Riyaancode` in a fresh session.
