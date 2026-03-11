# Data Load Overview

Loading Pendo Data Sync exports into a lakehouse follows a standard ETL pattern: extract from cloud storage, transform (parse and map schemas), and load into your warehouse tables.

## Data Load Flow

```
Pendo Data Sync  →  Cloud Storage (S3/GCS/Azure)  →  ETL Pipeline  →  Lakehouse Tables
```

## Concept Files

| Concept | Description |
|---------|-------------|
| [Export Overview](./export-overview.md) | How Pendo exports data, cloud destinations, export types, and file hierarchy |
| [ETL Pipeline](./etl-pipeline.md) | Extract, transform, and load design |
| [Data Load Strategy](./dataload-strategy.md) | Idempotency, finalized days, and retroactive processing |
| [Retroactive Processing](./retroactive-processing.md) | Why Pendo reprocesses historical data when Page/Feature rules change |
| [Finalized Data](./finalized-data.md) | Why event data is mutable for ~7–9 days and how to handle it |
| [Lakehouse Considerations](./lakehouse-considerations.md) | Lakehouse choice, partitioning, and Avro handling |

## Learn More

- [Pendo Academy Data Sync](https://academy.pendo.io/data-sync) - Learning resources on Data Sync
- [Pendo Data Sync GCP export loading example](https://github.com/pendo-io/data-sync-gcp-export-loading-example) - Sample code for loading from GCS into BigQuery
