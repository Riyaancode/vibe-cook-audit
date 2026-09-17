# Severity definitions

| Level | Definition | Examples |
|---|---|---|
| **Critical** | Exploitable now with real money, data, or account impact; or the product cannot function in production. | Unverified payment webhooks, cross-tenant data access, self-granted admin, secrets in the client bundle, database that cannot be rebuilt. |
| **High** | Serious defect or security gap that will bite real users but needs a precondition, or a core flow that is half-done. | Missing ownership check on a delete, no refund path, unthrottled endpoint that sends email, scheduled job never scheduled. |
| **Medium** | Correctness or hardening gap with limited blast radius; product contradictions visible to users. | Missing input validation, stale UI copy, unbounded list queries, plaintext tokens behind column grants. |
| **Low** | Quality, hygiene, performance polish, dead code, naming. | Lint failures, legacy names, unused exports, missing indexes on cold tables. |

## Publishing rule

Every Critical and High finding must be re-verified by the orchestrator
directly against the code (open the file, run the grep) before it appears
in `audit.md`. A finding that does not reproduce is dropped. A finding that
partially reproduces is downgraded and the note says what was and was not
confirmed. Reviewer agents can be wrong; the audit must not be.

## Blocker rule for the brief

A finding becomes a "launch blocker" in `audit-brief.md` only if it is
Critical, or High with money or personal-data impact. Everything else goes
under "important problems".
