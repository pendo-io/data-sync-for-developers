# data-sync-for-developers

Learn how to load and query product data from Pendo Data Sync into your lakehouse. This repository helps Data Engineers understand the end-to-end workflow-from Pendo exports to cloud storage, through ETL pipelines, and into analytics-ready tables.

## Purpose

Pendo Data Sync exports granular, event-level product usage data to cloud storage (Amazon S3, Google Cloud Storage, Microsoft Azure). This repository provides conceptual guidance and documentation to help you:

- Understand what Pendo Data Sync exports and how it works
- Design and implement data loading pipelines into your lakehouse
- Query and analyze Pendo data alongside your other business data

**Prerequisites:** Pendo Data Sync is a paid add-on. You must be a subscription admin in Pendo to configure cloud storage destinations and enable exports.

> [!CAUTION]
> Content in this repository is public domain/free use. Use is at your own discretion; the author provides no warranty, support, or service level guarantees.

## Repository Structure

```
data-sync-for-developers/
├── README.md           # This file
├── .data/              # Reference structure mirroring export hierarchy (see concepts/01-dataload)
└── concepts/           # Conceptual documentation (markdown only)
    ├── 00-pendo/       # Pendo and Data Sync fundamentals
    ├── 01-dataload/    # Loading data into your longterm storage
    └── 02-querying/    # Querying and analyzing synced data
```

## Concepts

| Area | Description |
|------|-------------|
| [Pendo](concepts/00-pendo/) | What Data Sync is, export formats, destinations, and data models |
| [Data Load](concepts/01-dataload/) | Export types and directory structure, ETL from cloud storage to lakehouse, idempotency (finalized days, retroactive processing), incremental vs full refresh |
| [Querying](concepts/02-querying/) | How to query Pendo data and combine it with other sources for analytics |
