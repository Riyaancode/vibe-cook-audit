# Convex reviewer checklist

If the `convex` plugin is installed, use the `convex:convex-reviewer` agent's rules or load its skills first.

1. Every query/mutation/action uses the object syntax with `args` and `returns` validators; no `v.any()`; no missing validators.
2. Auth: every public function verifies identity before DB access; internal functions not exposed as public; `internalMutation`/`internalQuery` used for server-only paths.
3. Ownership: functions that accept ids check the caller may access that document; multi-tenant fields (org/workspace) enforced.
4. Indexes: `.withIndex` used instead of `.filter` on large tables; compound indexes for multi-field queries; unbounded `.collect()`.
5. N+1 patterns in handlers; pagination on list endpoints.
6. Schema: soft-delete fields if the product needs them; index naming; `discardedAt`-style fields last in compound indexes.
7. HTTP actions: signature verification for webhooks; CORS; rate limiting.
8. Crons: defined and pointing at existing functions; idempotent.
9. File storage: upload URLs scoped; access checks on serve.
10. Environment: secrets via Convex env, not committed; `auth.config.ts` correct for the provider.
