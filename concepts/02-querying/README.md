# Querying Pendo Data Overview

Data Sync loads **raw event data** and definition tables into your lakehouse. To get metrics and segments that align with what you see in the Pendo UI—reports, dashboards, and analytics—you need to **query that raw data and replay the same transformations** Pendo applies: time zones, aggregation rules, exclude lists, and metadata handling. This section describes how to do that.

## Goal: Match the Pendo UI

When your SQL (or analytics tool) applies the same logic Pendo uses, you get:

- **Reproducible metrics** — Adoption, engagement, and funnel numbers that match Pendo’s.
- **Shared definitions** — Product and data teams can trust one source of truth.
- **Flexibility** — The same raw data can power custom breakdowns, joins to CRM/sales, and historical analysis beyond the UI.

## Querying flow

```
Raw event tables + definition/history tables
    → Apply time zones, exclude list, aggregation, metadata
    → Results that match the Pendo UI (or extend it)
```

You query the raw event and definition tables, then apply the transformations covered in the topic docs below.

## Topics in this section

| Topic | Description |
|-------|-------------|
| [Visitor and account metadata](visitor-account-metadata.md) | Deriving `VISITORMETADATA` and `ACCOUNTMETADATA` from raw Avro (unpivot via metadataschema) so segment and breakdown logic matches Pendo. |
| [Aggregation](aggregation.md) | How to aggregate events (e.g. by day, feature, segment) so counts and rates match Pendo reports. |
| [Time zones](timezones.md) | How timestamps and time zones work in Data Sync and how to bucket/group time so dates align with the Pendo UI. |
| [Exclude list](exclude-list.md) | Excluding visitors, accounts, or events (e.g. internal, test) so reported numbers match Pendo’s filters and exclude list. |

## Event and history tables (what you query)

The data you query falls into two categories:

- **Event tables** — Raw user interactions: `ALLEVENTS`, `MATCHEDPAGEEVENTS`, `MATCHEDFEATUREEVENTS`, `MATCHEDTRACKTYPEEVENTS`. One row per event (or per event×matchable for matched tables).
- **Definition / history tables** — Dimensions that change over time: visitors, accounts, pages, features, track types, guides. Use these to resolve names, metadata, and segment membership.

Most analyses that “match the UI” join events to these definition tables, then apply the same time-windowing, exclusions, and aggregation as Pendo.

## Beyond matching the UI

You can also use the same raw data for use cases that go beyond replicating Pendo:

- **Customer demographics** — Join `visitor_id` or `account_id` to CRM or identity data.
- **Marketing and sales** — Correlate touchpoints and revenue with in-app behavior.
- **Custom models** — Churn, journey, or cohort analysis with your own definitions.

Use a consistent identity key (`visitor_id`, `account_id`, or a mapped ID) across systems for reliable joins.

## Learn More

- [How Pendo uses Data Sync to unify teams with product data](https://www.pendo.io/pendo-blog/how-pendo-uses-data-sync-to-unify-teams-with-product-data/) — Real-world examples and insights
