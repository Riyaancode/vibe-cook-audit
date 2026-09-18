---
name: audit-workflow
description: Use when asked to audit a codebase, review production readiness, do a security or code review of a whole project, check what is finished or half-done, or produce an audit document for a client. Also use when /audit is invoked.
---

# Audit workflow

## Overview

Produces `audit.md` (technical) and `audit-brief.md` (client) for the project in the current working directory. The audit is only as trustworthy as its verification step: reviewer agents propose, the orchestrator confirms.

**Announce at start:** "Using audit-workflow to audit <project>."

## Recipe

Work through these in order. Create a todo per step.

1. **Detect the stack.** Read every manifest and the top-level folder list. Match against `references/service-registry.md`. Output two artefacts before doing anything else: the stack table (module → provider, major services only) and the list of reviewers to activate. Show the stack table in chat.

2. **Pre-flight plugin check.** For each detected service with a plugin id, check `~/.claude/plugins/installed_plugins.json` (or `ls ~/.claude/plugins/cache`). Print one line per service: installed ✅ or missing ❌ with the exact install commands from the registry. If anything is missing, say "Install the ones you want, then reply *continue* (or *continue without*)." and **stop the turn**. Do not start step 3 in the same turn. On resume, re-check installs, then proceed.

3. **Baseline checks.** Follow `references/baseline-checks.md`. Run in the background where slow. Save raw output to the scratchpad; it goes verbatim into the appendix.

4. **Dispatch reviewers.** Build each prompt from `references/agent-brief.md` with the matching checklist under `checklists/`. Use the plugin's agents (`structure-reviewer`, `security-reviewer`, `frontend-reviewer`, `dependency-reviewer`, plus the activated specialists). Dispatch all in one message, in the background. While they run, do the hygiene and size metrics from the baseline reference yourself.

5. **Verify.** For every Critical and High finding in the returned reports, open the file or run the grep and confirm it. Apply `references/severity.md`. Drop what does not reproduce; downgrade what partially reproduces; note "could not verify" for anything that needs live-system access. Record which findings were verified.

6. **Write `audit.md`** from `templates/audit.md` into the project root. Section order is fixed. Every finding in sections 5 and 6 has a matching row in Appendix A with file:line.

7. **Write `audit-brief.md`.** Load the `audit-brief-writing` skill and apply it to the `audit.md` you just wrote.

8. **Report in chat.** Verdict, the blockers in one line each, both file paths, and the items that could not be verified without live access. Offer `/estimate` as the next step.

## Hard rules

- Read-only against the project. The only files written are `audit.md`, `audit-brief.md` and scratch files outside the project.
- The pre-flight stop in step 2 is mandatory whenever a recommended plugin is missing.
- No Critical/High finding is published unverified. Reviewer output is evidence, not conclusion.
- "Could not verify" beats a guess. Live-database policies, external schedulers and dashboard settings are the usual cases; name them in the report.
- Every baseline check either has a result or a "could not run: reason". No silent skips.

## Red flags — stop and re-read the recipe

| Thought | Reality |
|---|---|
| "The plugin check is a formality, I'll keep going" | Specialist reviewers load those plugins' rules. Missing plugin = weaker audit. Stop and ask. |
| "The reviewer cited a line, that's verified" | Reviewers hallucinate line numbers and policy names. Open the file. |
| "This project is small, skip the parallel agents" | Small projects still have payments and auth. Run at least the four core reviewers. |
| "Build failed, I'll note it and move on" | A failed build is a finding *and* a blocker for the bundle secret scan. Diagnose the first error. |
| "I'll put hours in the brief" | The brief uses Low/Medium/High only. Hours belong to `/estimate`. |
