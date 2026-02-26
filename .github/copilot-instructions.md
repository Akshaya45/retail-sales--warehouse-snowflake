# Copilot Instructions for Retail Sales Warehouse (Snowflake)

This repository is a straightforward Snowflake-based ETL demo that builds a
bronze/silver/gold warehouse from the TPC‑DS sample dataset.  The entire project
is a collection of numbered SQL scripts and a few architecture diagrams.  The
instructions below are intended to get an AI coding agent up to speed quickly
when making changes or adding new examples.

---
## 🎯 Big‑Picture Architecture

1. **Bronze / Silver / Gold layers** – each schema in
   `SNOWFLAKE_LEARNING_DB` corresponds to a layer:
   * `BRONZE` stores raw copies of tables from
     `SNOWFLAKE_SAMPLE_DATA.TPCDS_SF100TCL` (STORE_SALES, CUSTOMER, etc.).
   * `SILVER` holds cleaned/enriched data, fact/dimension tables, and
     implements SCD‑2 logic.
   * `GOLD` contains aggregated/curated results used for reporting.

2. **Pipeline order** is encoded by filename prefixes in `sql/` (01_bronze.sql, 02_silver.sql,…).
   Reading them from 01 → 08 shows the stepwise refinement of data.

3. **SCD type‑2 & historical joins** are demonstrated in
   `04_scd.sql`, `05_incremental_load.sql`, and `07_historical_join.sql`.
   These files show how new customer states are staged, merged, and joined to
   facts.

4. **Diagrams** in `architecture/` (Mermaid `.mmd` files) provide nodes for
   each layer.  Use them to understand data flow and quality‑gate concepts.

---
## 🛠 Developer Workflows

* There is no build system; work is performed by executing the SQL scripts
  against a Snowflake account (use the web console or SnowSQL CLI).
* Default warehouse `COMPUTE_WH` and database `SNOWFLAKE_LEARNING_DB` are
  referenced in every script – ensure these exist or change them when adapting
  for another account.
* To recreate the project from scratch, open each SQL file in order and run the
  statements (e.g. copy‑paste into Snowflake worksheet).  The numbered files
  are written as runnable sessions with validation queries included.
* Add new example queries or tables by adding new numbered files following the
  same pattern (e.g. `09_my_new_step.sql`).  Keep the incremental order.
* There are no automated tests; verify changes by running the validation
  queries at the bottom of each script.

---
## 📁 Project‑Specific Conventions

* **Naming:** schemas are uppercase (`BRONZE`, `SILVER`, `GOLD`) and tables
  use capitalized snake case (`FACT_SALES`, `CUSTOMER_DIM`).
* **Tables:** most are created with `CREATE OR REPLACE TABLE` so scripts can be
  re‑run without dropping manually.
* **Stages:** temporary staging tables (e.g. `CUSTOMER_STAGE`, `FACT_STAGE`)
  mimic incoming batches and are usually truncated or recreated in the same
  script.
* **Comments:** scripts include section headers (`-- STEP 1:` etc.)—preserve
  these when adding new logic to maintain readability.
* **Architecture files:** use Mermaid syntax; updating the diagrams when adding
  layers or tables helps future contributors.

---
## 🔌 Integration Points & Dependencies

* The only external dependency is Snowflake itself and the built‑in sample
  dataset `SNOWFLAKE_SAMPLE_DATA.TPCDS_SF100TCL`.
* There is no application code or CI pipeline; all logic lives in SQL and
  documentation.
* When altering SQL to target a different database/warehouse, document the
  required substitutions in comments at the top of the affected script.

---
## ✅ Useful Patterns & Examples

* SCD merge block (from `04_scd.sql`):

```sql
MERGE INTO SILVER.CUSTOMER_DIM target
USING SILVER.CUSTOMER_STAGE source ...
```

* Historical fact load join (from `05_incremental_load.sql`):

```sql
SELECT ...
FROM SILVER.FACT_STAGE f
JOIN SILVER.CUSTOMER_DIM d
  ON ...
  AND (f.transaction_date < d.end_date OR d.end_date IS NULL);
```

* Aggregation examples in gold layer (sales by year, state, top stores).

---
## 📍 Where to Look First

* `sql/01_bronze.sql` – starts the pipeline, creates raw table.
* `sql/04_scd.sql` – introduces slowly changing dimension logic.
* `architecture/Medallian_overview.mmd` – big‑picture diagram of Bronze/
  Silver/Gold pattern.
* `README.md` – very brief project description and grain definition.

---
## ❓ Questions for the User

Please review the instructions above and let me know:

1. Are there any workflows (e.g. tooling, commands) that I missed or should
   document?
2. Do you have additional conventions (naming, commit messages) that an AI
   agent should follow when contributing?
3. Would you like example output from any of the SQL scripts included directly
   in the docs?

Thanks! 👍
