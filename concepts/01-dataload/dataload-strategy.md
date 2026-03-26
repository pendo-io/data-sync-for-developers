# Data Load Strategy

Create one `schema` per application for event and definition data, plus one `schema` each for visitor and account data. This layout most closely matches the exported Avro files.

```
DATABASE
│
├── S: SUB_{subscription_id}_ACCOUNT
│   ├── T: ACCOUNTS
│   └── T: ACCOUNTMETADATA
│
├── S: SUB_{subscription_id}_VISITOR
│   ├── T: VISITORS
│   └── T: VISITORMETADATA
│
└── S: SUB_{subscription_id}_APP_{application_id}
    ├── T: ALLEVENTS
    ├── T: MATCHEDPAGEEVENTS
    ├── T: MATCHEDFEATUREEVENTS
    ├── T: MATCHEDTRACKTYPEEVENTS
    ├── T: PAGES
    ├── T: FEATURES
    ├── T: TRACKTYPES
    └── T: GUIDES
```
`S: schema` , `T: table`

## Tables

| Table | Source | Primary Key | Partition Key |
|-------|--------|-------------|---------------|
| `ACCOUNTS` | `account/{export}/accounts.avro` | `(id, )` | - |
| `ACCOUNTMETADATA` | `account/{export}/metadataSchema.avro` | `(name, )` | - |
| `VISITORS` | `visitor/{export}/visitors.avro` | `(id, )` | - |
| `VISITORMETADATA` | `visitor/{export}/metadataSchema.avro` | `(name, )` | - |
| `ALLEVENTS` | `{app}/{export}/allEvents.avro` | `(periodId, eventId)` | `periodId` |
| `MATCHEDPAGEEVENTS` | `{app}/{export}/matchedEvents/Page/*.avro` | `(periodId, eventId, matchableId)` | `periodId` |
| `MATCHEDFEATUREEVENTS` | `{app}/{export}/matchedEvents/Feature/*.avro` | `(periodId, eventId, matchableId)` | `periodId` |
| `MATCHEDTRACKTYPEEVENTS` | `{app}/{export}/matchedEvents/TrackType/*.avro` | `(periodId, eventId, matchableId)` | `periodId` |
| `PAGES` | `{app}/{export}/allPages.avro` | `(pageId, )` | - |
| `FEATURES` | `{app}/{export}/allFeatures.avro` | `(featureId, )` | - |
| `TRACKTYPES` | `{app}/{export}/allTrackTypes.avro` | `(trackTypeId, )` | - |
| `GUIDES` | `{app}/{export}/allGuides.avro` | `(guideId, )` | - |

## Load Logic

### Event tables

Read the **bill of materials** and iterate over `timeDependent`. For each `periodId`:

**All Events**

- If `allEvents` is present: delete existing rows for this `periodId`, then insert from `allEvents.files` (`allEvents.avro`).
- If `allEvents` is absent (retroactive export): skip. Do not load `ALLEVENTS` for this period.

**Matched Page, Feature, and TrackType Events**

- For each entry in `matchedEvents`: use `id` as `matchableId` (e.g. `Page/q95zvVQ6m5wyBz7-FcgxsIKCeLw`).
- Delete existing rows where `(periodId, matchableId)` matches this period and matchable.
- If `files` has entries: insert from `files`.
- If `files` is empty: only delete. (Rules may have changed; the matchable no longer matches any events for this period.)

Pendo re-exports the same `periodId` for [finalized days](./finalized-data.md) and [retroactive processing](./retroactive-processing.md).

### Definition tables (Pages, Features, Guides, TrackTypes)

Truncate, then load from `pageDefinitionsFile`, `featureDefinitionsFile`, `guideDefinitionsFile`, `trackTypeDefinitionsFile`.

Full replace on each load. The latest definitions are in every export.

### Account and Visitor tables

Account and visitor exports use a different bill-of-materials shape: `accounts.files` / `visitors.files` and `metadataSchemaFile`.

Full replace on each load. No `timeDependent` block.

## Pre-Filtering

Filter during load to exclude unwanted rows. Document exclusions for downstream consumers. Some examples of why you may pre-filter data before loading to long-term storage:

- **Anonymous visitors** — Pendo assigns temporary IDs to unidentified users; most Pendo reporting revolves around identified users.
- **Align with Pendo analytics** — Data Sync exports all events, including those classified by the [exclude list](https://support.pendo.io/hc/en-us/articles/360032209171).
- **Cost and compliance** — Reduce storage and query cost, or exclude test accounts, internal users, or regions for privacy.

**Example: exclude anonymous visitors** (`visitorId` like `_PENDO_T_%`):

```sql
INSERT INTO bronze.ALLEVENTS (
    periodId
  , eventId
  , visitorId
  , ...
)
SELECT
    periodId
  , eventId
  , visitorId
  , ...
FROM
  staging.allevents_stage
WHERE
  visitorId NOT LIKE '_PENDO_T_%'
;
```

Or filter at query time via a view:

```sql
CREATE VIEW bronze.ALLEVENTS_IDENTIFIED
AS (
  SELECT
    *
  FROM
    bronze.ALLEVENTS
  WHERE
    visitorId NOT LIKE '_PENDO_T_%'
);
```

---
← [Data Load Overview](./README.md)
