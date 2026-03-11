# Pendo Data Sync Overview

Pendo Data Sync is an automated data export feature that transfers product usage data from Pendo into your cloud storage. As a Data Engineer, you can then load this data into a lakehouse (Databricks, Snowflake, BigQuery, etc.) for analysis alongside your other business data.

## What Data Sync Exports

Data Sync delivers **event-level data** with millisecond timestamps-not hourly aggregates. You get visibility into every user interaction: feature usage, page views, guide engagement, and custom track events. This granularity enables detailed journey analysis, adoption metrics, and churn modeling that would be impossible with aggregated data alone.

Exports are written in **Avro format**, which is well-suited for schema evolution and efficient processing in downstream pipelines. Avro files are compact and easy to parse with common data tools (Spark, Polars, DuckDB, etc.).

## Why It Matters for Lakehouse Pipelines

Data Sync breaks down silos by giving you a single source of truth for product usage. You can:

- Combine Pendo events with customer demographics, marketing campaigns, and sales data
- Build complete user journeys from marketing touchpoints through in-app behavior
- Support retroactive processing and historical analysis from the earliest export date

Data Sync keeps data synchronized automatically, so your warehouse stays aligned with Pendo without manual exports.

## Learn More

- [Pendo Data Sync product page](https://www.pendo.io/product/data-sync/) - Product overview and capabilities
- [Data Sync glossary](https://www.pendo.io/glossary/data-sync/) - Broader guide to data synchronization concepts
