# Source Schema v3 Migration Guide

Use this guide when converting legacy flat `schemaDefinition` payloads to v3.

## Current requirement

New contributor submissions must use:

- `schemaVersion: 3`
- `pipeline.calendar`
- `pipeline.event`

If missing, requests fail with: `schemaDefinition must be v3 with schemaVersion=3.`

## Flat -> v3 mapping

Legacy shape (flat HtmlLite example):

```json
{
  "eventCardSelector": "...",
  "mappings": {
    "title": "...",
    "startTime": "..."
  },
  "validation": {
    "requiredFields": ["title", "startTime"]
  }
}
```

v3 shape:

```json
{
  "schemaVersion": 3,
  "pipeline": {
    "calendar": {
      "type": "Html",
      "parser": {
        "eventCardSelector": "...",
        "mappings": {
          "title": "...",
          "startTime": "..."
        }
      }
    },
    "event": {
      "type": "Html",
      "input": {
        "mode": "calendarFieldUrl",
        "field": "eventUrl"
      },
      "parser": {
        "mappings": {}
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

## Migration checklist

1. Add `schemaVersion: 3`.
2. Move parser config under `pipeline.calendar.parser`.
3. Set `pipeline.calendar.type` to one of: `Ics`, `Rss`, `Json`, `Html`.
4. Add `pipeline.event` using an allowed pair from [Type Pair Reference](type-pair-reference.md).
5. If using `calendarFieldUrl`, map `eventUrl` in calendar mappings and set event input field to `eventUrl`.
6. Run `POST /api/source-schemas/test-fetch` before submission.

## Common migration mistakes

- Keeping flat fields at root (`eventCardSelector`, `mappings`) instead of inside `pipeline.calendar.parser`
- Using disallowed pair `Html -> None`
- Using `pipeline.event.input.field: "url"` instead of `"eventUrl"` in current phase-2 scaffolding
- Sending `schemaDefinition` as object instead of JSON string in request envelope
