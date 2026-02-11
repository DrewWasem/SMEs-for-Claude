# PostgreSQL Heuristics — Pattern-Matched Judgment

These are "when you see X, usually do Y" rules. Strong defaults, not absolute constraints.

## Schema Design Patterns

- **When tables exceed 1M rows:** Partition by time or logical grouping (e.g., ins_match_result partitioned by insight_id)
- **When tables have flexible metadata:** Use JSONB column with GIN index for frequent queries
- **When table has status workflows:** Add CHECK constraint with explicit enum values, plus status transition audit trail
- **When joining frequently:** Create covering index with all join + filter columns
- **When storing user-generated text:** Use TEXT not VARCHAR (no length overhead in PostgreSQL)
- **When storing embeddings:** Use `vector(384)` for COHERANY's standard model (all-MiniLM-L6-v2)

## Migration Best Practices

- **When adding columns:** Use `ALTER TABLE ADD COLUMN IF NOT EXISTS`, set sensible defaults
- **When changing constraints:** Use two migrations: (1) add new constraint with NOT VALID, (2) VALIDATE CONSTRAINT
- **When renaming columns:** Verify no code references old name (grep codebase), then use ALTER TABLE RENAME
- **When backfilling data:** Use batched updates with LIMIT/OFFSET to avoid lock contention
- **When dropping columns:** First deploy code that doesn't reference it, then drop in next migration
- **When migration fails:** Check rollback script exists at `sql_ddl/migrations/rollback/R{number}__*.sql`

## Index Optimization

- **When creating HNSW indexes:** Use M=32 (balance), ef_construction=100 (build), ef_search=128 (query)
- **When queries filter + sort:** Create composite index with filter columns first, then ORDER BY column
- **When table has high write volume:** Limit indexes to essential queries only (indexes slow writes)
- **When full-text search needed:** Use GIN index on `tsvector` column, not LIKE queries
- **When range queries on timestamps:** Create BRIN index for insert-ordered tables (compact, efficient)

## Row-Level Security (RLS)

- **When implementing multi-tenancy:** Enable RLS on all tenant-scoped tables, use `app.tenant_id` session variable
- **When creating RLS policies:** Use restrictive policies (`USING tenant_id = ...`), not permissive (`USING TRUE`)
- **When testing RLS:** Run queries with `SET app.tenant_id = '<uuid>'; SELECT ...` to verify isolation
- **When RLS causes FK issues:** Can't delete cfg_tenant when audit trigger enabled — skip deletion in tests or disable trigger
- **When superuser bypasses RLS:** Use service account with limited privileges (`coherany_app` role)

## Connection Pooling

- **When API server starts:** Initialize ThreadedConnectionPool with min=1, max=10 (adjustable via env vars)
- **When pool exhausted:** Auto-recovery service resets pool after 2-minute cooldown
- **When long-running queries:** Use separate connection, not from pool (risk of pool starvation)
- **When workers process batches:** Use direct connections with autocommit, not pooled (single-threaded)

## Performance Tuning

- **When query scans >1K rows:** Add index or refactor to use existing index
- **When query uses LIKE '%pattern%':** Replace with pg_trgm GIN index or full-text search
- **When N+1 queries detected:** Batch with `WHERE id IN (...)` or use JOIN with LATERAL
- **When aggregation on large table:** Use materialized view refreshed periodically
- **When slow query threshold exceeded:** Log with duration_ms in structured logs (`COHERANY_SLOW_QUERY_THRESHOLD_MS`)

## Partitioning Strategy

- **When partitioning by time:** Use monthly partitions for tables with 1-10M rows/month
- **When partitioning by ID:** Use hash partitioning for even distribution (e.g., by tenant_id modulo)
- **When creating partitions:** Use declarative partitioning (PARTITION OF), not inheritance
- **When querying partitions:** Ensure WHERE clause includes partition key for pruning
- **When partition maintenance needed:** Create automated scripts for partition creation + old partition archival

## Data Type Selection

- **When storing JSON:** Use JSONB (supports indexing), create GIN indexes on frequent access paths: `CREATE INDEX ON table USING gin (metadata jsonb_path_ops);`
- **When storing enums:** Use CHECK constraint for <10 values, PostgreSQL ENUM for >10 stable values
- **When storing money:** Use NUMERIC(10,2) or similar precision, never FLOAT
- **When storing flags:** Use BOOLEAN not SMALLINT/CHAR(1), PostgreSQL optimizes storage
- **When storing UUIDs:** Use UUID type not CHAR(36), saves 20 bytes per row

## Migration Rollback

- **When writing rollbacks:** Mirror forward migration exactly in reverse (CREATE → DROP, ALTER ADD → ALTER DROP)
- **When rollback has data loss:** Document in migration header, require manual confirmation
- **When rollback tested:** Test in dev environment before production deployment
- **When rolling back partitions:** Drop child partitions before parent table

## Query Optimization Patterns

- **When joining on UUIDs:** Ensure both sides indexed, PostgreSQL handles UUID joins efficiently
- **When querying with OR:** Rewrite as UNION ALL if branches access different indexes
- **When subquery in SELECT:** Often rewrite as LEFT JOIN for better performance
- **When counting large tables:** Use `SELECT reltuples FROM pg_class WHERE relname = 'table'` for estimate
- **When pagination needed:** Use LIMIT/OFFSET for small offsets (<1000), keyset pagination for large

## Extension Management

- **When using pgvector:** Ensure version 0.5.0+ installed (HNSW support)
- **When using pgcrypto:** Required for gen_random_uuid() in triggers
- **When using pg_trgm:** Required for similarity search on text columns
- **When adding extensions:** Use `CREATE EXTENSION IF NOT EXISTS`, check availability in target environment

## Trigger Design

- **When auto-populating columns:** Use BEFORE INSERT/UPDATE triggers (e.g., ai_meta UUID, updated_at)
- **When auditing changes:** Use AFTER INSERT/UPDATE/DELETE triggers to avoid interfering with main operation
- **When enqueueing work:** Use AFTER INSERT/UPDATE triggers with conditional logic (e.g., `NEW.status = 'pending'`)
- **When trigger modifies NEW:** Only valid in BEFORE triggers, AFTER triggers can't change data
- **When trigger calls functions:** Ensure function is idempotent and handles NULL inputs gracefully
