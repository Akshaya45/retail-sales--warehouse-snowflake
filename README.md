# retail-sales--warehouse-snowflake
End-to-end SQL Data Warehouse project using Snowflake, TPCDS dataset, Bronze-Silver-Gold architecture, SCD Type 2, and performance optimization.


## Fact Table Grain

FACT_SALES grain:
One row per customer per store per transaction date.

Measures:
- sales_price (additive)

## CI/CD Workflow

Branch Strategy:
- dev → Development
- main → Production-ready

Deployment Process:
1. Develop changes in dev branch.
2. Run data quality tests (09_data_quality_tests.sql).
3. Validate results.
4. Create Pull Request.
5. Merge to main after review.