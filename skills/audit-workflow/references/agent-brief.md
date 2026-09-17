# Reviewer agent brief

Every reviewer is dispatched with this prompt shape. Fill the angle brackets.
Dispatch all active reviewers in one message so they run in parallel.

```
You are auditing <area> of the project at "<absolute path>". Read-only: do NOT
modify any file. Use Bash/grep/cat and Read.

Stack summary: <stack table rows>. Package manager: <pm>.

Follow the checklist at <absolute path to checklist .md>. Read it fully
before starting.

<If an official plugin is installed for this service:> Before reviewing,
load the skill "<skill name>" with the Skill tool and apply its rules; cite
which rule a finding violates.

Scope: <one paragraph: which folders/files, what to cover, what to ignore
because another reviewer owns it>.

Report shape (markdown):
(A) Executive summary — 3 to 5 sentences with a verdict for this area.
(B) Findings table sorted Critical > High > Medium > Low. Columns: #,
    severity, file:line, description, impact, recommended fix. Cite exact
    lines. If you cannot confirm something, write "could not verify" instead
    of guessing.
(C) Inventory relevant to this area (functions, tables, routes, flows) —
    compact table with a status per row (Complete / Partial / Stub / Missing).
(D) What is good.
(E) Required environment variables and external configuration you found.

Be thorough over brief; cover everything in scope, not a sample.
```

## Ownership boundaries (so reviewers do not overlap)

| Reviewer | Owns | Leaves to others |
|---|---|---|
| structure-reviewer | folder layout, naming, file sizes, dead code, duplication, circular deps, docs quality, env var inventory | security findings, feature status |
| security-reviewer | server functions/endpoints auth and ownership, input validation, secrets exposure, injection/XSS/SSRF/redirects, rate limiting, PII in logs, account deletion | database policies (supabase/sql), payment logic |
| frontend-reviewer | routes and screens, feature completeness vs docs/plans, half-done indicators, error/loading states, hooks/lint issues, bundle/perf, PWA/SEO, accessibility sample | server security |
| dependency-reviewer | vulnerability audit, outdated/major drift, license flags, duplicate/overlapping libraries, lockfile and package manager, beta pins | usage bugs |
| stripe-reviewer | every Stripe call, webhook, money flow, Connect, billing, tax | non-payment code |
| supabase-reviewer | migrations, RLS, SECURITY DEFINER, views, triggers, storage, cron, config | application code |
| convex-reviewer | schema, function validators, auth in functions, indexes, crons, http actions | frontend |
| firebase-reviewer | security rules, functions auth, storage rules, config exposure | frontend |
| sql-orm-reviewer | schema, migrations, constraints, indexes, query safety, connection handling | RLS if Supabase (owned by supabase-reviewer) |
| mongodb-reviewer | schema/validation, indexes, injection, connection handling | — |
| auth-provider-reviewer | provider wiring, session handling, role/claims usage, webhooks from the provider | RLS |
| mobile-reviewer | native shell/config, deep links, push, store requirements, offline, permissions | web routes |
| messaging-reviewer | email/SMS senders, templates, consent/unsubscribe, inbound webhooks, rate limits | — |
| cloud-reviewer | IAM, buckets, serverless config, secrets management, infra-as-code | app logic |
