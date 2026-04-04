# Html Detail Enrichment: Choosing One Pattern

Html sources can be enriched from detail pages in two ways.

## Pattern A: Calendar parser detailPage

Configured in `pipeline.calendar.parser.detailPage` (HtmlLite parser behavior).

Use when:

- You already have stable HtmlLite selectors and field transforms.
- You need direct detail mappings and transform chaining in calendar parsing flow.

## Pattern B: Event stage Html parser

Configured in `pipeline.event.type = "Html"` with `pipeline.event.parser.mappings`.

Use when:

- You want enrichment logic in the explicit event stage.
- You want one consistent v3 stage model across source types.

## Do not run both patterns for the same fields

Using both patterns can trigger duplicate detail fetches and confusing overwrite behavior.

Recommendation:

- Pick one primary enrichment path per source.
- If migrating, disable old detail mappings before enabling equivalent event-stage mappings.

## Minimal pattern examples

Pattern A:

```json
"calendar": {
  "type": "Html",
  "parser": {
    "eventCardSelector": ".event",
    "mappings": {
      "title": "h2",
      "startTime": "literal:2026-01-01",
      "eventUrl": "a@href"
    },
    "detailPage": {
      "enabled": true,
      "detailMappings": {
        "startTime": ".detail-date",
        "description": ".detail-body"
      }
    }
  }
}
```

Pattern B:

```json
"event": {
  "type": "Html",
  "input": {
    "mode": "calendarFieldUrl",
    "field": "eventUrl"
  },
  "parser": {
    "mappings": {
      "description": ".detail-body",
      "imageUrl": ".hero img@src"
    }
  },
  "limits": {
    "sameHostOnly": true,
    "maxRequestsPerRun": 5
  }
}
```
