Data engineering · Lakehouse design pattern

# Medallion Architecture: Bronze, Silver, Gold

Data moves through three layers, and each one raises the quality bar. Raw data lands once, gets cleaned once, and is shaped for the business once. Every dashboard and every model then draws from the same trusted tables.

## How the data flows

## What each layer promises

### Bronze RAW

Keep everything, exactly as it arrived.

- Append-only copy of each source, never edited
- Adds load time, source file and batch id
- Tolerates bad rows and schema drift

Bar

It landed and it is complete

Users

Data engineers

Example

`bronze.orders_raw` as JSON

### Silver CLEANED

One clean, conformed version of each entity.

- Deduplicate, cast types, handle nulls
- Validate with data-quality checks
- Join sources into customers, orders, products
- Where row-level features get built

Bar

Correct, typed, deduplicated

Users

Analysts, data scientists

Example

`silver.orders` with one row per order

### Gold CURATED

Shaped to answer business questions.

- Aggregates and KPIs, such as revenue by month
- Dimensional models for BI tools
- Point-in-time feature tables for ML training

Bar

Agreed definitions, fit to decide on

Users

Leaders, BI, ML pipelines

Example

`gold.customer_churn_features`

## Why it works better

### Lineage and traceability

Any Gold number can be traced back through Silver to the exact raw record. **When a KPI looks wrong, you can find out why.**

### Reprocess from raw

Raw data is never overwritten. Fix a bug or change a rule, then **rebuild downstream tables** without going back to the source systems.

### A clear quality bar per layer

Each layer has a contract. Checks run at each hop, so **bad data is stopped before it reaches a dashboard** or a model.

### Separation of concerns

Ingestion, cleaning and business logic live in different places. **Teams change one layer without breaking the others**, and each step is easy to test.

### One source of truth

"Active customer" and "revenue" are defined once in Gold. **Sales, finance and product see the same number** in every meeting.

### Data that is ready for ML

Features come from clean Silver tables and versioned Gold training sets. **Training data is reproducible**, and training matches serving.

## Where should this table live?

Is it untouched source data?Bronze

Is it cleaned, but still row-level?Silver

Does it answer a business question?Gold

Common stacks: Databricks with Delta Lake, Snowflake, or BigQuery with dbt. The pattern stays the same on any of them.