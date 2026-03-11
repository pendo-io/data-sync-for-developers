# Querying Pendo Data Overview

Once Pendo Data Sync data is loaded into your lakehouse, you can query it with SQL or your analytics tool of choice. This document covers how to think about querying Pendo data and common use cases.

## Event and History Tables

Pendo data falls into two categories:

- **Event tables** - Rows for each user interaction (feature usage, page views, guide engagement, and custom track events).
- **History tables** - Dimension data (accounts, visitors, features, guides, pages) that change over time.

Most analyses combine events with history tables. For example, join `FEATURE_EVENT` to the features history table to get feature names and metadata, or join events to `visitors` to enrich with user attributes.

## Common Use Cases

| Use Case | Approach |
|----------|----------|
| **Feature adoption** | Aggregate `FEATURE_EVENT` by feature and time period; join to features for names. |
| **User journeys** | Sequence events by `visitor_id` and timestamp; optionally join to pages/features for context. |
| **Churn prediction** | Use event recency and frequency; combine with account/visitor attributes. |
| **Guide effectiveness** | Analyze `GUIDE_EVENT` for views, completions, and dismissals by segment. |

## Combining with Other Data

Data Sync’s value increases when you combine Pendo data with other sources:

- **Customer demographics** - Join `visitor_id` or `account_id` to your CRM or identity data.
- **Marketing data** - Correlate marketing touchpoints with in-app behavior.
- **Sales data** - Link product usage to conversion and revenue.

Use a consistent identity key (eg. `visitor_id`, `account_id`, or a mapped ID) across systems for reliable joins.

## Learn More

- [How Pendo uses Data Sync to unify teams with product data](https://www.pendo.io/pendo-blog/how-pendo-uses-data-sync-to-unify-teams-with-product-data/) - Real-world examples and insights
