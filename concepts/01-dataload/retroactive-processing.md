# Retroactive Processing

When you add or update rules for Pages or Features in Pendo, Pendo reprocesses historical event data to apply the new or changed tags. This triggers a **retroactive export** to be included in the next sync.

The export contains the lifetime of data since Pendo was installed for the changed Page or Feature, **not** just recent days.

> [!IMPORTANT]
> The raw event stream (`allEvents.avro`) does not change. Pendo reprocesses **which events match which Pages / Features**.

### Pendo's Rule-Based Matching Model

- The [Pendo Agent](../00-pendo/pendo-agent.md) captures raw events client-side and sends them to Pendo. Each event has a `browserTimestamp` and `url`.
- **Pages** are defined by URL matching rules (eg. `//*/dashboard`, `//*/admin/**`).
- **Features** are defined by DOM / selector rules (eg. a button with a specific id).
- **Matched events** are produced by applying these rules to raw events.
    - Raw events live in `allevents.avro`
    - Matched Pages live in a filtered view in `matchedEvents/Page/<id>.avro`
    - Matched Features live in a filtered view in `matchedEvents/Feature/<id>.avro`

### Why Rules Change

- **New Pages or Features** - Product analytics needs evolve; you add tracking for new flows.
- **Refined rules** - URL or DOM patterns are broadened or narrowed (eg. fixing a typo or adding a wildcard).
- **Corrections** - Rules are fixed after initial setup.

---

## Data Handling

Follow the idempotency pattern detailed in [dataload strategy](./dataload-strategy.md).

- **Merge by `periodId`** - Drop existing event data for that `(periodId, matchableId)`, then load the new files.
- **Empty list case** - If a period has an empty `matchedEvents` list for a Page or Feature, drop the data for that period.
- **Frequency** - Pendo initiates retroactive exports up to once per day when rules change.

---

**Related:** [Export Overview](./export-overview.md) (types of exports) · [Finalized Data](./finalized-data.md) · [Data Load Strategy](./dataload-strategy.md) · [Data Sync event export handling](https://support.pendo.io/hc/en-us/articles/14617105854875-Data-Sync-event-export-handling)

---
← [Data Load Overview](./README.md)
