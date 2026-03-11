# Data Load Strategy

Pendo Data Sync re-exports data in specific scenarios. Your ETL pipeline must handle these updates idempotently to avoid duplicates and keep your lakehouse aligned with Pendo.

## Finalized Days

Event data is not finalized immediately. Session data can arrive late (eg. a user leaves a browser tab open, or a device crashes before sending). Pendo finalizes event data approximately **7–9 days** after collection.

### How Recurring Exports Work

Each daily recurring export includes **two** distinct days of data:

1. **Yesterday's data** - Unfinalized; may change in a future export.
2. **Finalized data** - Typically 8–10 days old; replaces the previously unfinalized export for that day.

| Export Date | Yesterday's Data | Finalized Data |
|-------------|------------------|----------------|
| April 15, 2024 | April 14, 2024 | April 7, 2024 (replaces unfinalized export from April 8) |

### Load Strategy

When you receive data for a `periodId` that already exists in your warehouse, **drop the existing data for that period** and load the new data. This applies to both yesterday's updates and finalized replacements.

## Retroactive Processing

When you add or update rules for Pages or Features in Pendo, Pendo reprocesses historical data to apply the new or changed tags. This triggers a **retroactive export** for the affected Pages and Features.

### Characteristics

- Retroactive exports use the same schema as regular exports.
- They do **not** include `allevents.avro` - only the reprocessed matched event files.
- Definition files for the changed Pages or Features are included.
- Retroactive exports appear in the Pendo UI as created by `--system-user--`.

### Load Strategy

Use the same drop-and-replace logic as for finalized days: if you receive data for a `periodId` already loaded, drop the event data for that period (for the affected matchables) and load the new files. If you receive an empty list for a period, drop the data for that period.

> [!NOTE]
> Renaming a Page or Feature in Pendo does not trigger a retroactive export. The updated name appears in the definition file in the next daily export.

For more on why retroactive processing happens and Pendo's architecture, see [Retroactive Processing](./retroactive-processing.md).

## Idempotency

Design your loads so re-running the pipeline does not create duplicates.

### Events

- **Key:** `(periodId, eventId, matchableId)` - Use this combination for upsert or as the scope for drop-and-replace.
- **Strategy:** For each `periodId` in an export, delete existing rows for that period, then insert the new data. This handles finalized days, retroactive exports, and pipeline re-runs.

### Accounts and Visitors

- **Key:** `accountId` or `visitorId`
- **Strategy:** For ongoing metadata updates, drop existing rows for each `accountId`/`visitorId` in the export, then insert the new data. Full replace per record ensures consistency.

### Definition Files

- **Strategy:** Replace all data in definition tables (Pages, Features, Guides, Track Events) with each export. The latest definitions are sent in every export.

## Incremental vs Full Refresh

| Approach | When to Use |
|----------|-------------|
| **Incremental** | Recurring or ongoing exports. Process only new exports (track via manifest `counter`). For each export, apply drop-and-replace by `periodId` or by entity ID. |
| **Full refresh** | One-time backfill or full rebuild. Load all exports from scratch. Still use drop-and-replace within each export to handle overlapping periods. |

Incremental loads are the norm for production. Full refresh is useful for initial backfill or disaster recovery.

## Related Documentation

- [Data Sync event export handling](https://support.pendo.io/hc/en-us/articles/14617105854875-Data-Sync-event-export-handling) - Updates to exported data
- [Export Overview](./export-overview.md) - File hierarchy and bill of materials
- [ETL Pipeline](./etl-pipeline.md) - Extract, transform, and load design
