# Auth provider reviewer checklist (Clerk, Auth0, Auth.js, WorkOS, custom)

If a provider plugin is installed (e.g. `core@clerk-skills`), load its skills first.

1. Server-side verification of the session/JWT on every protected entry point; not just client-side route guards.
2. Roles and permissions: stored where users cannot edit them (not public/unsafe metadata); checked server-side; admin cannot be self-assigned.
3. Provider webhooks (user created/deleted) verified by signature; idempotent; keep local user table in sync.
4. Session lifetime, refresh, revocation on password change/delete; multi-device sign-out.
5. Callback/redirect URLs validated; `next`/`returnTo` restricted to same-site paths.
6. OAuth flows use `state`/PKCE; native/mobile flows configured separately.
7. Email verification and password policy; magic link / OTP brute-force protection.
8. Account deletion and export paths exist and are wired in the UI.
9. Secrets (client secret, webhook secret) server-only.
