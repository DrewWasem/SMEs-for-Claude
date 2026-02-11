# PostgreSQL Glossary

## Database Terms

**Connection Pool**: Pre-initialized set of database connections reused across requests (ThreadedConnectionPool in COHERANY)

**HNSW (Hierarchical Navigable Small World)**: pgvector index type for approximate nearest neighbor search, optimized for >100K vectors

**IVFFlat**: pgvector index type for smaller datasets (<100K vectors), uses inverted file with flat compression

**Partition Pruning**: Query optimization that skips scanning partitions not matching WHERE clause conditions

**RLS (Row-Level Security)**: PostgreSQL feature enforcing row-level access control based on policies, used for multi-tenancy

**JSONB**: Binary JSON storage format with indexing support (preferred over JSON in PostgreSQL)

**GIN Index**: Generalized Inverted Index, optimized for JSONB, arrays, and full-text search

**BRIN Index**: Block Range Index, compact index for insert-ordered tables with correlated physical and logical ordering

**Declarative Partitioning**: Modern partitioning syntax using `PARTITION OF` (vs legacy inheritance-based partitioning)

**Operator Class**: Specifies distance metric for vector indexes (cosine, L2, inner product)

**pgcrypto**: PostgreSQL extension providing cryptographic functions including `gen_random_uuid()`

**Session Variable**: Runtime configuration set with `SET app.tenant_id = '...'`, scoped to connection lifetime

## COHERANY-Specific Terms

**Classification Source**: Phase 1 data table used for clustering and insight creation (`source_type='classification'`)

**Pipeline Source**: Phase 2 data table used for production inference (`source_type='pipeline'`)

**Insight**: Production pattern for matching new records (stored in `ins_pattern`)

**Draft Insight**: Pending approval pattern from clustering (stored in `ins_draft_pattern`)

**Match Versioning**: Never delete matches, use `is_latest` flag and `superseded_at` timestamp

**Sealing**: Marking a classification table as complete for processing (prevents embedding workers from processing incomplete uploads)

**Prototype**: Demo use case configuration (stored in `proto_pattern`, NEVER approved, loaded ONE AT A TIME)

**ai_meta**: JSONB column storing UUID, processing flags, and custom metadata on source tables

**Work Queue**: Job tracking system (`wq_job`, `wq_job_history`) for embedding and inference tasks

**FAISS Manifest**: Metadata tracking FAISS index shards (`emb_faiss_manifest`)

**Scope Run**: Clustering execution instance (`clust_scope_run`) with parameters and quality metrics

**Unmatched Record**: Record with similarity below threshold (stored in `ins_unmatched` for reprocessing)

**Text Composition**: Combining multiple short columns into composite text for embedding (solves <10 char minimum)

**Fail-Closed**: RLS pattern using NULLIF to deny access when tenant_id not set (vs fail-open)

**Pooled Connection**: Context manager pattern for acquiring/releasing connections from pool

**Auto-Seal**: Automatic sealing of classification tables after CSV upload completes

**Auto-Enqueue**: Automatic FAISS job creation after embedding completion for pipeline tables

**Quality Gates**: Clustering validation thresholds (max noise %, min clusters, max largest cluster %)
