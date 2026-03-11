# Finalized Data

Pendo event data is not finalized immediately. Data Sync exports include both unfinalized (recent) and finalized (older) data. The [Pendo Agent](../00-pendo/pendo-agent.md) captures events client-side (clicks and page views) and sends them to Pendo. Each event has a `browserTimestamp` (when it occurred).

Events can arrive late for a few reasons.

- **Browser sessions** - A user leaves a tab open; session data from "yesterday" may not be sent until "today."
- **Device or network issues** - Crashes, offline usage, or connectivity problems delay event transmission.
- **Mobile or background apps** - Events are queued and sent when the app is next foregrounded.
- **Batch or deferred sends** - The SDK may buffer events before sending.

> [!IMPORTANT]
> Because of this, Pendo waits **~7-9 days** before treating a day's data as final.

Each daily recurring export typically includes multiple periods of data.

| Export Date | Yesterday (unfinalized) | Finalized (up to 10 days ago) |
|-------------|:-----------------------:|:-----------------------------:|
| 2024/04/15  | 2024/04/14              | 2024/04/05 ++                 |

---

## Data Handling

- **Merge events by `periodId`** - If data for a `periodId` already exists, delete it and load the new data.
- **Multiple exported periods** - The same period can appear in multiple exports (unfinalized, then finalized)
- **Recent data is mutable** - Last ~10 days can change. Use finalized data only for complete analytics, or document the lag.

**Related:** [Data Load Strategy](./dataload-strategy.md) · [Retroactive Processing](./retroactive-processing.md) · [Export Overview](./export-overview.md)