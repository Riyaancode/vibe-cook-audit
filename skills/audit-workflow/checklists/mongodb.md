# MongoDB reviewer checklist

1. Schema validation (Mongoose schemas or JSON schema) on every collection; strict mode.
2. Indexes for every query pattern; unique indexes on tokens/emails; TTL indexes for expiring data.
3. Injection: user input passed as query objects (`{$where}`, operators from request bodies); sanitisation.
4. Multi-tenancy fields enforced in every query.
5. Unbounded `find()` without limit; pagination.
6. Connection pooling and reuse in serverless.
7. Transactions for multi-document writes that must be atomic.
8. Secrets and connection strings not committed; IP allow-list documented.
