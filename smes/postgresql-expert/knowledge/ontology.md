# PostgreSQL Ontology for COHERANY

## Table Prefixes

| Prefix | Layer | Purpose | Examples |
|--------|-------|---------|----------|
| `cfg_` | Configuration | System configuration and metadata | `cfg_source_table`, `cfg_tenant`, `cfg_api_key` |
| `wq_` | Work Queue | Job scheduling and tracking | `wq_job`, `wq_load_batch`, `wq_job_history` |
| `raw_` | Raw Data | Unprocessed source data | `raw_conversation`, `raw_message` |
| `emb_` | Embeddings | Vector embeddings and FAISS manifests | `emb_vector`, `emb_faiss_manifest` |
| `stg_` | Staging | Temporary worker staging tables | `stg_emb_0` through `stg_emb_7` |
| `clust_` | Clustering | HDBSCAN clustering results | `clust_scope_run`, `clust_topic`, `clust_assignment` |
| `ins_` | Insights | Pattern library and matches | `ins_pattern`, `ins_draft_pattern`, `ins_match_result` |
| `wfl_` | Workflow | Approval workflows | `wfl_decision`, `wfl_approval` |
| `proto_` | Prototypes | Demo use cases | `proto_pattern` |
| `audit_` | Audit | Compliance and tracking | `audit_log`, `audit_tenant_usage` |
| `data_*` | Classification | Phase 1 source tables | `data_insurance_claims` |
| `pipeline_*` | Pipeline | Phase 2 production tables | `pipeline_insurance_claims` |

## Key Tables

### Core Configuration
- **cfg_source_table**: Registers tables for AI processing (classification or pipeline type)
- **cfg_embedding_model**: Defines available embedding models (dimension, max_tokens)
- **cfg_tenant**: Multi-tenant isolation root
- **cfg_api_key**: API authentication and rate limiting

### Work Queue
- **wq_job**: Active jobs (composite PK: source_table_id, source_pk, model_name, prompt_version)
- **wq_job_history**: Completed job archive with retry_count, last_error
- **wq_load_batch**: Batch grouping for pipeline processing

### Embeddings
- **emb_vector**: 384-dim vectors with HNSW index (pgvector)
- **emb_faiss_manifest**: FAISS index file metadata (shard tracking)

### Clustering
- **clust_scope_run**: Clustering run metadata (min_cluster_size, metric, total_records)
- **clust_topic**: Cluster centroids (cluster_id, topic_name, member_count)
- **clust_assignment**: Record-to-cluster mapping (cluster_id, membership_probability)

### Insights
- **ins_pattern**: Production insights (min_similarity threshold, source/target tables)
- **ins_draft_pattern**: Pending approval (cluster_id, submitted_at, reviewed_at)
- **ins_match_result**: Partitioned match storage (is_latest versioning, partitioned by insight_id)
- **ins_unmatched**: Near-misses and unmatched records (best_similarity tracking)

### Prototypes
- **proto_pattern**: Demo use case definitions (NEVER approved, loaded ONE AT A TIME)

## PostgreSQL Extensions

| Extension | Version | Purpose |
|-----------|---------|---------|
| `pgvector` | 0.5.0+ | Vector similarity search with HNSW indexes |
| `pgcrypto` | Any | UUID generation via `gen_random_uuid()` |
| `pg_trgm` | Any | Trigram similarity for text search |

## Common Patterns

### Two-Phase Architecture
1. **Classification Source** (`source_type='classification'`): Phase 1 clustering creates insights
2. **Pipeline Source** (`source_type='pipeline'`): Phase 2 matches against insights

### Match Versioning
- `is_latest = TRUE`: Current match for a record
- `is_latest = FALSE, superseded_at != NULL`: Historical match
- Never DELETE matches, always version with UPDATE

### Partitioning
- `ins_match_result`: Partitioned by `insight_id` (range partitioning)
- `audit_log`: Partitioned by `created_at` (monthly range partitioning)
- Indexes auto-inherit from parent (don't manually create on partitions)

### Row-Level Security (RLS)
- Multi-tenant tables filtered by `app.tenant_id` session variable
- Fail-closed pattern: `NULLIF(current_setting('app.tenant_id', true), '')::uuid`
- Superuser role bypasses RLS (use `coherany_app` service account)

### JSONB Metadata
- `ai_meta` column: Stores UUID, processing flags, custom metadata
- GIN indexes for fast JSONB queries: `CREATE INDEX ON table USING gin (ai_meta);`
- Path-specific access: `ai_meta->>'uuid'`, `ai_meta->'flags'`
