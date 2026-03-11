# Export Overview

Pendo Data Sync copies your raw product usage data out of Pendo and delivers it to your cloud storage. Understanding what gets exported, where it lands, and how exports are organized is the first step in building your data loading pipeline. For more, see the [Overview of Pendo Data Sync](https://support.pendo.io/hc/en-us/articles/18214274061595-Overview-of-Pendo-Data-Sync).

## What Data Sync Exports

Data Sync exports three categories of data:

- **Event data** - Raw and matched events (Pages, Features, Tracks), guide interactions, and poll responses. Configured at the application level.
- **Account metadata** - Account-level attributes and their associated metadata. Configured at the subscription level.
- **Visitor metadata** - Visitor-level attributes and their associated metadata. Configured at the subscription level.

All exports are written as **Avro files** plus an **export manifest** (`exportmanifest.json`) that your ETL pipeline uses to discover and load new data.

## Export Format

- **Avro** - Compact, schema-aware format suitable for streaming and batch processing.
- **Export manifest** - JSON file that lists recent exports and provides metadata for ETL automation.
- **Bill of materials** - JSON file that lists all files within a given export.

## Cloud Destinations

Data Sync supports three cloud storage providers.

| Provider | Authentication | Guide |
|----------|----------------|:---------------:|
| **Amazon S3** | IAM user access keys or IAM roles | [setup](https://support.pendo.io/hc/en-us/articles/14653887341083-Set-up-Data-Sync-with-Amazon-S3-using-user-access-keys) |
| **Google Cloud Storage** | Service account JSON key | [setup](https://support.pendo.io/hc/en-us/articles/13929263615643-Set-up-Data-Sync-with-Google-Cloud) |
| **Microsoft Azure Storage** | App registration (client ID + secret) | [setup](https://support.pendo.io/hc/en-us/articles/17099552655771-Set-up-Data-Sync-with-Microsoft-Azure-Storage) |

### Setup Steps

1. Create storage (bucket or container) in your cloud provider.
2. Create credentials (service account, IAM user, or app registration).
3. Grant write access to the storage.
4. In Pendo, go to **Settings > Data Sync** and configure the destination with credentials and path.
5. Validate the connection.

Credentials can be rotated manually in the Pendo UI or via the [Pendo public API](https://engageapi.pendo.io/#bed8e316-ba10-4909-98c2-2ec23a7d265f).

## Export Types

Your ETL pipeline will encounter these export types. All use the same directory structure and schema; the difference is what triggers them and what data they contain.

| Export Type | Data | Trigger | Contents |
|-------------|------|---------|----------|
| **Historical** | Events | One-time backfill | Full event data for the date range (up to 3 years sequentially) |
| **Recurring** | Events | Daily schedule | Yesterday's data (unfinalized) + finalized data from up to 9 days ago |
| **Finalized** | Events | Part of recurring export | Re-export of a day's data after ~7–9 days; replaces an earlier `periodId` |
| **Retroactive** | Events | Updates to Pages/Features | Matched event files for affected Pages/Features only (no `allevents.avro`) |
| **Account** | Accounts | Daily schedule | Account metadata and metadataschema |
| **Visitor** | Visitors | Daily schedule | Visitor metadata and metadataschema |

**Finalized** and **retroactive** exports both re-export previously seen `periodId`s and require drop-and-replace by `periodId` to avoid duplicates. See [Finalized Data](./finalized-data.md) and [Retroactive Processing](./retroactive-processing.md) for why each happens and how to handle them.

## Directory Structure

In cloud storage, the root path you configure (eg. `gs://bucket/` or `s3://bucket/`) is prepended; Pendo creates a `datasync` folder under that root.

This repository includes a [`.data/`](../../.data/) folder that mirrors the export structure for reference.

```
.data/datasync/{subscription-id}/
├── {application-id}/
│   ├── exportmanifest.json            # Rolling 30-day window of exports; metadata about each export
│   ├── {export-uuid}/
│   │   ├── billofmaterials.json       # Documents export contents; paths to definition and event files
│   .   ├── allevents.avro             # All event data, including unmatched events and guide events
│   .   ├── allfeatures.avro           # Feature definitions
│   .   ├── allguides.avro             # Guide definitions
│   .   ├── allpages.avro              # Page definitions
│   .   ├── alltracktypes.avro         # Track Event definitions
│   .   └── matchedEvents/
│   .       ├── Feature/
│   .       │   └── <feature_id>.avro  # Events matched to a specific Feature
│   .       ├── Page/
│   .       │   └── <page_id>.avro     # Events matched to a specific Page
│   .       └── TrackType/
│   │           └── <track_id>.avro    # Events matched to a specific Track Event
│   │
│   └── {export-uuid}/
│       └── ...
│
├── account/
│   ├── exportmanifest.json            # Rolling 30-day window of exports; metadata about each export
│   └── {export-id}/
│       ├── billofmaterials.json
│       ├── accounts.avro              # Account definitions
│       └── metadataschema.avro        # Schema for metadata fields (names, types, groups)
│
└── visitor/
    ├── exportmanifest.json            # Rolling 30-day window of exports; metadata about each export
    └── {export-id}/
        ├── billofmaterials.json
        ├── visitors.avro              # Visitor definitions
        └── metadataschema.avro        # Schema for metadata fields (names, types, groups)
```

---
← [Data Load Overview](./README.md)
