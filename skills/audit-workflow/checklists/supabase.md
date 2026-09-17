# Supabase reviewer checklist

If the `supabase` plugin is installed, load `supabase:supabase` and `supabase:supabase-postgres-best-practices` first. Concatenate migrations in filename order into a scratch file and reason about the FINAL state (later `CREATE OR REPLACE`/`DROP` wins).

1. Baseline: does the first migration create the core tables, or does it `ALTER` tables that already exist? If the base schema is missing from the repo, that is High: list the tables/functions referenced but never created (cross-check with generated `types.ts`).
2. RLS: every table in exposed schemas has `ENABLE ROW LEVEL SECURITY`; tables with RLS but zero policies (intentional service-role-only, or a mistake?); tables without RLS (Critical).
3. Policy quality: `USING (true)` on writes or on sensitive reads; `TO authenticated` with no ownership predicate (BOLA); UPDATE without `WITH CHECK`; `auth.jwt()` reading `user_metadata`; deprecated `auth.role()`; bare `auth.uid()` instead of `(select auth.uid())` on hot tables; broad `FOR ALL` granting delete on records that must be retained.
4. Failed hardening: for every `DROP POLICY IF EXISTS`, confirm the named policy actually existed. A drop of a non-existent name followed by a "tighter" policy leaves the old permissive policy live (policies are OR'd). Report each occurrence with the still-live policy.
5. `SECURITY DEFINER` functions: inventory; `search_path` pinned; `EXECUTE` revoked from `PUBLIC`/`anon` where not needed; internal `auth.uid()`/ownership check for every one callable by `authenticated`; any that mutate roles, subscriptions, credits, bookings or payouts without a guard.
6. Views without `security_invoker = true`.
7. Triggers on `auth.users`: role provisioning from `raw_user_meta_data` (can `admin` or a privileged role be minted?), hard-coded emails or ids.
8. Roles model: can a user insert their own role row? Is the INSERT policy on the roles table in the repo? Which roles can be self-selected?
9. Ledgers claimed append-only: does the trigger block INSERT as well as UPDATE/DELETE? Do client INSERT policies exist that defeat the intent (fee bypass, balance corruption)?
10. Storage: buckets defined in SQL? Public vs private; `storage.objects` policies per bucket and prefix; `TO authenticated` present; path-based ownership; size and MIME limits.
11. Grants: `GRANT ... TO anon` on tables/functions; column-level grants hiding secrets (good); functions created after a lockdown migration that regained default `PUBLIC` execute.
12. Cron/pg_net: what is scheduled, what URL it hits, where the secret comes from (never a literal in SQL), which job endpoints in the app have no schedule.
13. Secrets at rest: OAuth/refresh tokens, API keys stored plaintext.
14. Indexes: FK columns without indexes; RLS predicate columns indexed; unbounded append-only tables without retention.
15. Data integrity: unique constraints on tokens; expiry columns on invites; FK `ON DELETE` behaviour; enum drift vs generated types; migration churn (objects redefined many times) as a stability signal.
16. Hard-coded values in SQL: emails, UUIDs, URLs.
17. `config.toml` completeness for local dev; seed file; whether `supabase db reset` could work.
18. Compare with any backend inventory doc in the repo and list discrepancies.
