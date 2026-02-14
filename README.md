Databricks Lakehouse Medallion Pipeline (DLT + Auto Loader)
An end-to-end Databricks Lakehouse pipeline implementing the Medallion architecture (Bronze–Silver–Gold) with Delta Live Tables (DLT), incremental ingestion via Auto Loader, data quality enforcement, and production-style orchestration and observability.

Architecture
<img width="14204" height="4628" alt="architecture" src="https://github.com/user-attachments/assets/ef0899b2-e51e-4063-88f8-4bb72cc06c79" />



Flow (high level): Source system (trips) → application + relational database → data extract service → object storage landing zone (S3) → Databricks DLT Bronze → Silver → Gold → consumption (dashboards / conversational analytics).
Key capabilities

Incremental ingestion using Auto Loader with support for schema evolution and idempotent reprocessing.
DLT-managed pipelines implementing Bronze, Silver, and Gold layers with clear lineage.
Historical tracking via SCD Type 2 logic (MERGE-based) for slowly changing dimensions.
Data quality controls using DLT expectations, including quarantine handling for invalid records.
Delta optimization through partitioning and compaction to improve performance and manage cost.
Operationalization using Databricks Workflows for scheduling/retries plus monitoring via DLT event logs.

Medallion layers (what each layer represents)

Bronze: Raw, append-friendly ingestion from the landing zone; minimal transformations; preserves source fidelity.
Silver: Cleaned and conformed data; deduplication, standardization, and business-rule validation.
Gold: Curated, analytics-ready tables (facts/dimensions or aggregates) optimized for BI and downstream consumption.

Data quality and quarantine
DLT expectations are used to:

enforce schema and business rules,
route failing records to a quarantine path/table for audit and remediation,
keep the “good path” reliable for downstream consumers.

SCD Type 2 (historical tracking)
The pipeline maintains history for dimension-like entities by:

identifying natural keys,
tracking effective date ranges and current flags,
creating new “versions” of a record when tracked attributes change.

Orchestration, retries, and observability

Databricks Workflows orchestrate runs, retries, and dependencies.
DLT event logs provide operational visibility into pipeline health, data quality outcomes, and processing metrics.

Performance optimization approach
Typical optimizations applied to Delta tables include:

partitioning on high-selectivity, commonly filtered columns,
compaction to reduce small files and improve scan efficiency,
right-sizing compute and scheduling to control cost.

Suggested repository structure

pipelines

dlt_bronze (ingestion definitions)
dlt_silver (cleansing + conformance)
dlt_gold (curation + aggregates)


schemas (optional reference schemas and contracts)
expectations (data quality rules and quarantine strategy)
workflows (job/pipeline orchestration definitions)
docs (notes, runbooks, design decisions)
architecture.png

How to run (at a glance)

Configure access to the landing zone (S3) and create a target catalog/schema in Databricks.
Create a DLT pipeline pointing to the Bronze/Silver/Gold definitions in this repo.
Set pipeline parameters (paths, table names, checkpoint locations, and schema evolution settings).
Trigger the pipeline via Databricks Workflows (scheduled) or run on demand.
Validate outputs by checking Gold tables and reviewing DLT event logs and quarantine tables.

Tech stack
Databricks, PySpark, Delta Lake, Delta Live Tables (DLT), Auto Loader, SQL
