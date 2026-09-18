# Raw effort bands

Raw hours are what a senior engineer familiar with the stack would take by
hand, including their own testing. These are the INPUT to calibration, never
the number written in the estimate.

| Task type | Typical raw hours | Signals |
|---|---|---|
| Contained fix | 1–4 | One file or one policy; the finding names the exact line; no design decision. |
| Guarded change | 4–10 | Several call sites, needs a test path (ownership checks across functions, validation on a family of endpoints). |
| Integration / payment work | 8–24 | Touches an external provider (Stripe, Twilio, auth), webhooks, retries, sandbox testing. |
| Broad cleanup | 6–20 | Formatting, lint, dead code, renames, docs; wide but shallow. |
| New feature (small screen or endpoint) | 8–20 | Something that does not exist yet with a clear spec. |
| Schema / data work | 4–16 | Migrations, constraints, backfills, exports and rebuild verification. |
| Testing pass | 6–16 | End-to-end verification of a whole area (all money flows, all auth paths). |
| Docs / config | 2–6 | Setup guides, env examples, dashboard configuration. |

Split any task over ~20 raw hours into parts; a single large row invites an arbitrary discount.
