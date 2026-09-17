# Messaging reviewer checklist (email, SMS, push delivery)

If the `twilio-developer-kit` or `resend` plugin is installed, load their skills first.

1. Inventory every sender: provider, file, what triggers it, whether it no-ops or throws when credentials are missing, and whether the failure is visible to the user.
2. Duplicate senders or two providers for the same channel; which one is live.
3. Inbound webhooks (STOP/START, bounces, replies, lead forms): signature verification, fail-closed when secret unset.
4. Consent and compliance: opt-in records, unsubscribe links with signed tokens, suppression list honoured before every send, STOP footer on marketing SMS, sender address/domain configured.
5. Rate limits and abuse: anonymous endpoints that can trigger sends; per-address caps; bulk invite loops.
6. Templates: user content escaped; personal data minimised; from/support addresses consistent.
7. Automations and sequences: scheduler exists and is wired; triggers query columns that exist; dead automations.
8. Deliverability config documented (SPF/DKIM/DMARC, verified senders, A2P registration for SMS).
9. PII in logs from send paths.
