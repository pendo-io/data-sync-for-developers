# Data Load Overview

Loading Pendo Data Sync exports into a lakehouse follows a standard ETL pattern: extract from cloud storage, transform (parse and map schemas), and load into your warehouse tables.

## Data Load Flow

```
Pendo Data Sync  →  Cloud Storage (S3/GCS/Azure)  →  ETL Pipeline  →  Long-term Storage
```

## Key Concepts

- **Export structure** — Avro files, **export manifest**, **bill of materials**; data under `{app}/`, `account/`, `visitor/`.
- **Schema layout** — One schema per app for events/defs; separate for visitors/accounts, matching export hierarchy.
- **Idempotent loads** — Re-exported event data (finalized/retroactive); delete by `periodId`/`matchableId` before insert.
- **Definition tables** — Full replace on load; account/visitor exports use a different **bill of materials** shape than app exports.

## Concept Files

| Concept | Description |
|---------|-------------|
| [Export Overview](./export-overview.md) | Export types, cloud destinations, directory structure. See [`.data/`](../../.data/) for samples. |
| [Data Load Strategy](./dataload-strategy.md) | Schema layout, table mappings, load logic, pre-filtering. |
| [Finalized Data](./finalized-data.md) | Why event data is mutable for ~7–9 days; merge by `periodId`. |
| [Retroactive Processing](./retroactive-processing.md) | Why Pendo reprocesses when Page/Feature rules change; matched events only. |

## Learn More

* [Pendo Academy Data Sync](https://academy.pendo.io/data-sync) — Learning resources on Data Sync
* [Pendo Data Sync GCP export loading example](https://github.com/pendo-io/data-sync-gcp-export-loading-example) — Sample code for loading from GCS into BigQuery
