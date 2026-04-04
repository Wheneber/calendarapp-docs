# Source Schema v3 Pipeline Examples

## Minimal Valid Schemas by Type

Use these templates as a starting point. Add fields incrementally and validate after each change.

### Html -> Html (Simplest Valid Html Pipeline)

Use when the calendar/list source is HTML. For v3, `Html -> None` is not allowed by the current security allowlist.

> Note: During current phase-2 scaffolding, `pipeline.event.input.field` must be `eventUrl`.

```json
{
  "schemaVersion": 3,
  "pipeline": {
    "calendar": {
      "type": "Html",
      "fetch": {
        "requestHeaders": {
          "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"
        }
      },
      "parser": {
        "eventCardSelector": ".event-item",
        "mappings": {
          "title": "h2",
          "startTime": ".date",
          "eventUrl": "a@href"
        }
      }
    },
    "event": {
      "type": "Html",
      "input": { "mode": "calendarFieldUrl", "field": "eventUrl" },
      "parser": { "mappings": {} },
      "limits": {
        "sameHostOnly": true,
        "maxRequestsPerRun": 5
      }
    }
  },
  "validation": {
    "requiredFields": ["title", "startTime"]
  }
}
```

### Ics -> None

Use `Ics -> None` when the ICS feed provides complete event data and no HTML detail-page enrichment is needed. This is the correct combination for most ICS sources.

> **Common mistake:** Setting `event.type` to `"Html"` for a plain ICS feed that requires no detail-page fetch will fail validation. Use `"None"` unless you need to follow `eventUrl` to fetch per-event HTML pages.

```json
{
  "schemaVersion": 3,
  "pipeline": {
    "calendar": {
      "type": "Ics",
      "parser": {}
    },
    "event": {
      "type": "None",
      "input": {
        "mode": "none"
      },
      "parser": {}
    }
  },
  "validation": {
    "requiredFields": ["title", "startTime"]
  }
}
```

## Ics -> Html

```json
{
  "schemaVersion": 3,
  "pipeline": {
    "calendar": {
      "type": "Ics",
      "parser": {}
    },
    "event": {
      "type": "Html",
      "input": {
        "mode": "calendarFieldUrl",
        "field": "eventUrl"
      },
      "parser": {
        "mappings": {
          "description": ".event-details",
          "venueAddress": ".venue-address",
          "imageUrl": ".hero img@src"
        }
      },
      "limits": {
        "sameHostOnly": true,
        "maxRequestsPerRun": 5
      }
    }
  },
  "validation": {
    "requiredFields": ["title", "startTime"]
  }
}
```

## Json -> Json

```json
{
  "schemaVersion": 3,
  "pipeline": {
    "calendar": {
      "type": "Json",
      "parser": {
        "eventArrayPath": "$.events[*]",
        "mappings": {
          "title": "$.name",
          "startTime": "$.start",
          "eventUrl": "$.detailUrl"
        }
      }
    },
    "event": {
      "type": "Json",
      "input": {
        "mode": "calendarFieldUrl",
        "field": "eventUrl"
      },
      "parser": {
        "eventArrayPath": "$.items[*]",
        "mappings": {
          "description": "$.description",
          "venueName": "$.venue.name",
          "venueAddress": "$.venue.address"
        }
      },
      "limits": {
        "sameHostOnly": true,
        "maxRequestsPerRun": 10
      }
    }
  },
  "validation": {
    "requiredFields": ["title", "startTime"]
  }
}
```
