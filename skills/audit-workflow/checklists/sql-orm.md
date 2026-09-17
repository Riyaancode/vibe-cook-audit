# SQL / ORM reviewer checklist (Prisma, Drizzle, Neon, plain Postgres/MySQL)

1. Migrations: present, ordered, replayable from empty; schema file matches migrations; no manual prod edits implied.
2. Constraints: primary keys, unique on natural keys and tokens, FKs with explicit `ON DELETE`, check constraints, NOT NULL where the app assumes it.
3. Indexes on FK and filter columns; missing composite indexes for common queries; unbounded queries (`findMany` without `take`).
4. Query safety: raw SQL with interpolation; `$queryRawUnsafe`; string-built filters.
5. Multi-tenancy: tenant id on every table that needs it and enforced in every query (or via RLS).
6. Connection handling: pooling for serverless (Neon pooler, Prisma Accelerate/pgbouncer), connection limits, timeouts.
7. Secrets: `DATABASE_URL` not committed; separate dev/prod databases.
8. Data lifecycle: soft delete vs hard delete for regulated data; retention jobs; PII columns encrypted where required.
9. Transactions where multi-step writes must be atomic (payments, credits, inventory).
10. Seed and local-dev story.
