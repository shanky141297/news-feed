# Feed contract, version 1

Locked on 16 Nov 2026 and amended the same day to add `stories` to briefings, before any real entries existed. The website and the apps read this, and the scheduled briefing tasks write it. If the shape changes in a way that breaks readers, bump `version` first.

## Files

- `feed/latest.json`: the last 48 hours. This is the only file the app loads on open.
- `feed/archive/YYYY-MM-DD.json`: one file per UK calendar day, same shape, for older entries.
- `feed/sample.json`: design and test data, the same shape as `latest.json`. The app never loads it and the scheduled tasks never write to it. The briefing entries hold real titles, links, headlines and stories. The hourly update entry is made up for illustration, built from real stories.

## Top level

| Field | Type | Notes |
| --- | --- | --- |
| `version` | number | Always `1` for this contract |
| `lastUpdated` | string | ISO 8601 UTC, e.g. `2026-11-16T18:45:00Z`; equals the newest entry's `generatedAt` |
| `entries` | array | Newest first. The latest briefing is the first entry whose `type` is `"briefing"` |

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
| `stories` | Story[] | Every story in the briefing, in the briefing's order, most important first within each category |

## Entry: `"type": "update"` (hourly, planned)

| Field | Type | Notes |
| --- | --- | --- |
| `id` | string | `update-` + `generatedAt`, e.g. `update-2026-11-16T12:00:00Z` |
| `type` | string | `"update"` |
| `date` | string | UK calendar date, `YYYY-MM-DD` |
| `generatedAt` | string | ISO 8601 UTC |
| `title` | string | e.g. `Midday Update · 16 November 2026` |
| `items` | Story[] | Same shape as a briefing's stories |

## Story (used by `stories` and `items`)

| Field | Type | Notes |
| --- | --- | --- |
| `headline` | string | Plain text |
| `summary` | string | What happened, plain text, one to three sentences |
| `whyItMatters` | string | Optional. Plain text, one or two sentences |
| `category` | string | One of `world`, `uk`, `india`, `immigration`, `sport`, `space`, `markets` |
| `badge` | string | Optional. One of `breaking`, `developing`, `confirmed`, `proposal`, `unconfirmed`, `status`, `update` |
| `sourceUrl` | string | Must start with `https://` |

The category filter in the app is built from the categories that appear in `stories`. "All" is the unfiltered view, not a stored category.

## Rules for writers

- Write only under `feed/`.
- Skip the write if an entry with the same `id` exists.
- Move entries older than 48 hours into that day's archive file.
- All text is plain text: no HTML, no Markdown. The app renders everything as text, never as HTML.
