# PostgreSQL Hard Constraints — NEVER Violate

These rules are non-negotiable. Any work that violates these fails review.

## Schema Design

- **NEVER** use `SELECT *` in production queries or views
- **ALWAYS** include explicit column lists in SELECT statements
- **NEVER** create tables without a PRIMARY KEY
- **ALWAYS** use foreign keys with explicit ON DELETE/ON UPDATE actions
- **NEVER** create indexes without checking if they already exist (use IF NOT EXISTS)
- **ALWAYS** create indexes on foreign key columns
- **NEVER** use SERIAL or BIGSERIAL in partitioned tables (use sequences instead)

## Migrations

- **NEVER** create a migration without a corresponding rollback script
- **ALWAYS** name migrations with V{number}__{description}.sql format
- **NEVER** modify existing migrations that have been deployed
- **ALWAYS** use transactions (BEGIN/COMMIT) unless DDL requires it disabled
- **NEVER** drop tables without CASCADE if you're certain no dependencies exist
- **ALWAYS** check for conflicting migration numbers (V021 collision was a real bug)

## Row-Level Security (RLS)

- **NEVER** use permissive RLS policies that could leak data
- **ALWAYS** use fail-closed patterns: `USING (tenant_id = current_setting('app.tenant_id')::uuid)`
- **NEVER** use `USING (TRUE)` or empty policies on tenant-scoped tables
- **ALWAYS** use `NULLIF` to fail closed: `NULLIF(current_setting('app.tenant_id', true), '')::uuid`
- **NEVER** skip enabling RLS on multi-tenant tables (ALTER TABLE ENABLE ROW LEVEL SECURITY)

## pgvector

- **NEVER** create vector columns without specifying dimension: `vector(384)`
- **ALWAYS** use HNSW indexes for >100K vectors: `USING hnsw (embedding vector_cosine_ops)`
- **NEVER** use IVFFlat for small datasets (<100K vectors)
- **ALWAYS** specify operator class: `vector_cosine_ops`, `vector_l2_ops`, or `vector_ip_ops`
- **NEVER** create HNSW indexes on empty tables (build after data load)

## Partitioning

- **NEVER** manually create indexes on partitions if they auto-inherit from parent
- **ALWAYS** create partitions with `PARTITION OF parent_table` syntax
- **NEVER** create unique constraints on partitioned tables without the partition key
- **ALWAYS** use partial indexes on partitioned tables: `WHERE is_latest = TRUE`
- **NEVER** modify partition structure without updating partition maintenance scripts

## Connection Pooling

- **NEVER** open connections without closing them (use context managers)
- **ALWAYS** use psycopg2.pool.ThreadedConnectionPool for API servers
- **NEVER** set pool size > database max_connections
- **ALWAYS** configure `COHERANY_DB_POOL_MIN` and `COHERANY_DB_POOL_MAX`
- **NEVER** use connections outside of `with pooled_connection() as conn:` pattern

## Data Types

- **NEVER** use `TEXT` for constrained values (use ENUM or CHECK constraints)
- **ALWAYS** use `JSONB` not `JSON` for metadata (supports indexing)
- **NEVER** use `TIMESTAMP` without timezone (use `TIMESTAMPTZ`)
- **ALWAYS** use `UUID` for external IDs (not SERIAL)
- **NEVER** store decimals as FLOAT/REAL (use NUMERIC/DECIMAL for precision)

## Audit & Compliance

- **NEVER** allow DELETE on audit_log table (immutable trigger enforces this)
- **ALWAYS** use triggers for audit logging (don't rely on application code)
- **NEVER** backdate timestamps in audit tables
- **ALWAYS** include `created_at`, `updated_at` on all business tables
- **NEVER** skip audit logs for sensitive operations (tenant purge, data export)
