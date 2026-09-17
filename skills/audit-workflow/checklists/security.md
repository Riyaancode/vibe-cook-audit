# Security reviewer checklist (application layer)

Enumerate first, then judge. Cite file:line for every finding and include an exploit scenario.

1. Enumerate EVERY server entry point: server functions, API routes, RPC handlers, webhooks, cron/job routes, edge functions. Produce a table: name, file:line, HTTP method, auth mechanism, uses privileged/service-role client?, ownership or role check?, input validation?, verdict.
2. For each entry point that uses a privileged client (service role, admin SDK, raw DB connection): does it verify the caller first? Does it verify the caller owns or may act on the target row (IDOR)? Does it trust a user id / org id / coach id supplied by the client?
3. Admin authorisation: how is admin determined? Enforced server-side or only by routing? Can a user self-assign a privileged role (client-side role inserts, signup metadata, mutable user metadata)?
4. Public token routes (share links, invites, calendar feeds, unsubscribe, widgets): token entropy, uniqueness, expiry, revocation, what data each exposes, enumerability.
5. Inbound webhooks: signature verification present and correct for each provider (Stripe, Twilio, Meta, GitHub, provider auth emails)? Timing-safe comparisons? Fail-closed when the secret is unset?
6. Input validation: functions with pass-through or type-only validators; unbounded strings, arrays, JSON blobs; dates parsed without validation.
7. Injection and content: XSS (`dangerouslySetInnerHTML`, raw HTML in emails/templates), SQL/NoSQL/filter-string injection (string-built `.or()` filters, raw queries), path traversal in storage paths, SSRF in any server-side fetch of user-supplied URLs, open redirects on auth callbacks.
8. Secrets: env names read in files that ship to the client; top-level imports of server-only modules from client-reachable files; committed `.env`; keys in source; keys in logs or error messages.
9. Rate limiting and abuse: anonymous endpoints that send email/SMS, create accounts, or write rows; brute-forceable token or OTP checks; expensive compute endpoints.
10. Error handling: raw database/library errors returned to the browser; auth failures returned as 500 instead of 401/403.
11. Logging: PII (emails, phones, tokens) in `console.*` or log calls.
12. Uploads: MIME allow-list, size caps, server-generated paths, ownership links.
13. Account deletion / data export: caller bound to identity, confirmation, completeness across providers (payments, storage, auth).
14. CSRF protection on mutating endpoints; CORS settings on public routes; security headers (CSP, HSTS, frame options) presence.
