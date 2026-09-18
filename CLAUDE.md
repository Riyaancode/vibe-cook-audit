# CLAUDE.md — vibe-cook-audit

Claude Code plugin that audits a codebase and produces client-ready documents. Markdown only, no runtime code. Published at `github.com/Riyaancode/vibe-cook-audit`; the repo is its own marketplace (`Riyaancode`).

## Purpose and vision

Turn the manual audit-to-estimate process (first done on the Skill IQ project, September 2026) into something anyone can install with two commands and run on any stack. The audit must be **trustworthy** (findings verified against code, never just agent output), **client-readable** (a brief with no code terms), and **priced** (an estimate calibrated to Claude-assisted execution). Generic core, stack specialists activated by detection, official vendor plugins recommended before the audit starts.

## Commands → skills → outputs

| Command | Skill | Writes | Notes |
|---|---|---|---|
| `/taste` | `quick-scan` | `taste.md` | Minutes. No agents. Never pauses. Not an audit. |
| `/cook` | `audit-workflow` | `audit.md`, `audit-brief.md` | Full. Pauses after the plugin pre-flight if anything is missing. |
| `/plate` | `audit-brief-writing` | `audit-brief.md` | Re-run only; needs `audit.md`. |
| `/bill` | `estimate-writing` | `estimate.md` | Needs the brief, never the full audit. |

Commands in `commands/` are thin: load the skill, pass `$ARGUMENTS`. All logic lives in `skills/`.

## Layout

```
.claude-plugin/        plugin.json + marketplace.json (keep versions in sync)
commands/              taste, cook, plate, bill
skills/
  audit-workflow/      SKILL.md, references/, checklists/ (one per reviewer), templates/audit.md
  quick-scan/          SKILL.md, templates/taste.md  (reuses audit-workflow references)
  audit-brief-writing/ SKILL.md, templates/audit-brief.md
  estimate-writing/    SKILL.md, references/effort-bands.md, references/calibration.md, templates/estimate.md
agents/                14 read-only reviewers; each points at its checklist and an optional vendor plugin skill
docs/superpowers/      design spec and implementation plan (history, not instructions)
```

## Design rules (do not break)

- **Verification before publication.** Every Critical/High finding is re-checked by the orchestrator against the code. Reviewer agents propose; they never publish. Keep this in `audit-workflow` step 5 and `references/severity.md`.
- **Pre-flight stop.** `/cook` stops the turn when a recommended vendor plugin is missing. `/taste` never stops. Do not soften either.
- **Brief has no code terms.** No paths, identifiers, line numbers, package names, HTTP codes, time units. Effort is Low/Medium/High only. See the translation table in `audit-brief-writing`.
- **Calibration stays hidden.** `estimate-writing/references/calibration.md` factors and task-type labels never appear in `estimate.md`. Optional phase budgets (`phaseN=<hours>`) rescale calibrated rows proportionally to an exact sum.
- **Read-only.** The audited project is never modified except for the output files (and its own build output).
- **Registry is the single source of stack knowledge.** Detection, stack table, reviewer activation and plugin recommendations all come from `audit-workflow/references/service-registry.md`. Add services there, nowhere else.
- **Skill descriptions start with "Use when"** and describe triggers only, never the workflow (agents follow a summarised description instead of reading the skill).
- **Ownership boundaries** between reviewers are in `references/agent-brief.md`. A new reviewer needs: an agent file, a checklist, a registry row, and a boundary line.

## Working on this repo

1. Edit in this folder. Never edit the installed copy under `~/.claude/plugins/cache`.
2. Validate before committing: `for t in .claude-plugin/plugin.json .claude-plugin/marketplace.json skills agents commands; do claude plugin validate $t --strict; done`
3. Commit with a conventional prefix (`feat:`, `fix(skill-name):`, `docs:`), push to `main`.
4. Refresh the local install: `claude plugin update vibe-cook-audit@Riyaancode` (or uninstall/reinstall).
5. Bump `version` in both manifests and tag (`git tag vX.Y.Z && git push --tags`) for meaningful changes.

## Testing a skill change

Skills are tested by dispatching a subagent that reads the skill files directly and applies them to a real project, writing outputs to a scratch folder, and ending with a "## Skill feedback" section listing every ambiguity or guess. Fold that feedback back into the skill, re-run, commit. Reference project used so far: Skill IQ (TanStack Start + Supabase + Stripe + Capacitor). The four existing test shapes:

- **A** stack detection + pre-flight (steps 1–2 of `audit-workflow` only)
- **B** `estimate-writing` with `phase2=40 exclude=p4`: exact subtotal, no leaked calibration terms, phase 4 listed under Not included
- **C** `audit-brief-writing`: grep checks pass, blockers = audit Critical items, real client questions
- **D** `quick-scan` with `no-install`: no pauses, no severity words, under ~70 lines

## Tuning after real projects

- `estimate-writing/references/calibration.md`: append a row with raw vs actual hours per task type; move each factor halfway toward the observed ratio.
- `audit-workflow/references/service-registry.md`: new services, renamed plugins, new source signals (env names, hostnames).
- `audit-workflow/checklists/*.md`: add checks that caught real issues; remove ones that never fire.

## Out of scope (v1)

xlsx output, CI integration, auto-fixing findings, a web dashboard.
