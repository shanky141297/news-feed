# Feed contract, version 1

Locked on 16 Nov 2026. The website and the apps read this, and the scheduled briefing tasks write it. If the shape changes, bump `version` first.

## Files

- `feed/latest.json`: the last 48 hours. This is the only file the app loads on open.
- `feed/archive/YYYY-MM-DD.json`: one file per UK calendar day, same shape, for older entries.

## Top level

| Field | Type | Notes |
| --- | --- | --- |
| `version` | number | Always `1` for this contract |
| `lastUpdated` | string | ISO 8601 UTC, e.g. `2026-11-16T18:45:00Z`; equals the newest entry's `generatedAt` |
| `entries` | array | Newest first |

## Entry: `"type": "briefing"`

| Field | Type | Notes |
| --- | --- | --- |
| `id` | string | `YYYY-MM-DD-morning` or `YYYY-MM-DD-evening`; never repeats |
| `type` | string | `"briefing"` |
| `edition` | string | `"morning"` or `"evening"` |
| `date` | string | UK calendar date, `YYYY-MM-DD` |
| `generatedAt` | string | ISO 8601 UTC, the time the run wrote the entry |
| `title` | string | `Morning Briefing · 16 November 2026` (day without a leading zero, full month name) |
| `artifactUrl` | string | Must start with `https://claude.ai/artifact/` |
| `headlines` | string[] | The five must-know headlines, plain text |

## Entry: `"type": "update"` (hourly, planned)

| Field | Type | Notes |
| --- | --- | --- |
| `id` | string | `update-` + `generatedAt`, e.g. `update-2026-11-16T12:00:00Z` |
| `type` | string | `"update"` |
| `date` | string | UK calendar date, `YYYY-MM-DD` |
| `generatedAt` | string | ISO 8601 UTC |
| `title` | string | e.g. `Midday Update · 16 November 2026` |
| `items` | object[] | See below |

### Item

| Field | Type | Notes |
| --- | --- | --- |
| `headline` | string | Plain text |
| `summary` | string | Plain text, one or two sentences |
| `category` | string | One of `world`, `uk`, `india`, `immigration`, `sport`, `space`, `markets` |
| `badge` | string | One of `breaking`, `developing`, `confirmed`, `proposal`, `unconfirmed`, `update` |
| `sourceUrl` | string | Must start with `https://` |

## Rules for writers

- Write only under `feed/`.
- Skip the write if an entry with the same `id` exists.
- Move entries older than 48 hours into that day's archive file.
- All text is plain text: no HTML, no Markdown. The app renders everything with `textContent`.
