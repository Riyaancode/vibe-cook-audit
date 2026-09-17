# Cloud reviewer checklist (AWS, GCP, Azure)

If `aws-core` (or the equivalent) plugin is installed, load its IAM/secrets/serverless skills first.

1. Credentials: no long-lived keys in code or `.env`; roles/workload identity used; least privilege on every policy.
2. Buckets/storage: public access blocked unless intended; signed URLs with short expiry; encryption at rest; lifecycle rules.
3. Serverless/functions: auth on every HTTP trigger; timeouts and memory set; secrets from a secrets manager not env literals.
4. Infrastructure as code present and matches what is deployed; environments separated.
5. Logging and monitoring: errors alerting somewhere; PII not logged.
6. Cost/limits: unbounded triggers, missing concurrency limits, missing budgets.
7. Network: security groups/firewall rules, database not publicly reachable.
