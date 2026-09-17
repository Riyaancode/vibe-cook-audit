# Stripe reviewer checklist

If the `stripe` plugin is installed, load `stripe:stripe-best-practices` first and read its payments, billing, connect and security references. Cite the rule for each finding.

1. Client instantiation: `new Stripe(key, { apiVersion, appInfo })` with a pinned current API version; key from env only; recommend a restricted key (`rk_`). No global api_key pattern. No keys in source, logs or error messages.
2. Webhook: `constructEvent` on the raw body with a signing secret; separate secret/endpoint for Connect events; processed-event-id table for idempotency; return 200 for unhandled types; check `livemode`; map `event.account` for connected accounts. Flag any alternative such as re-fetching events by id.
3. Events handled: `checkout.session.completed`, `checkout.session.async_payment_succeeded`, `checkout.session.async_payment_failed`, `checkout.session.expired`, `customer.subscription.created/updated/deleted`, `invoice.paid`, `invoice.payment_failed`, `charge.refunded`, `charge.dispute.created`, `account.updated`. List which are missing and the consequence.
4. Fulfilment driven by webhooks, gated on `payment_status`, not by the success page. Success-page confirmation is acceptable only as a fallback.
5. `payment_method_types` never passed (except Terminal). `integration_identifier` on `checkout.sessions.create` where API version allows.
6. Amounts computed server-side from DB rows; client passes ids only. Currency handling. Metadata links every Stripe object back to a DB row.
7. Idempotency keys on every mutating call (sessions, customers, subscriptions, invoices, invoice items, transfers, accounts, refunds). Duplicate-subscription guard server-side.
8. Subscriptions: Products one per tier; Prices not deprecated Plans; Customer Portal; proration and downgrade scheduling; dunning; metered usage reporting idempotent; period bounds read from Stripe, not computed locally.
9. Metadata collisions: does any sync keyed on a metadata field (e.g. `coach_id`, `user_id`) also match subscriptions of a different kind? Trace every place that key is set.
10. Connect: Accounts v2 vs legacy `type:`; capability status vs legacy `charges_enabled`/`payouts_enabled`; charge pattern (direct / destination / separate charges+transfers) vs who the code and UI claim is merchant of record; `application_fee_amount` used only with direct/destination; `on_behalf_of`; hosted onboarding; OAuth `state` if OAuth.
11. Refund and cancellation paths exist for every money flow; app-side cancel reaches Stripe; transfer reversals for destination charges.
12. Expiry: sessions get `expires_at` where they hold inventory/slots; expired sessions release what they held.
13. Redirect URLs from configuration, not request `Origin`/`Host` headers.
14. Tax: `automatic_tax` presence; registration assumption; note for the client.
15. Scheduled billing jobs (overage, invoicing, dunning enforcement) actually scheduled somewhere.
16. Completeness matrix per money flow: Implemented / Partial / Stub / Missing with evidence; list every env var and Dashboard setting required.
