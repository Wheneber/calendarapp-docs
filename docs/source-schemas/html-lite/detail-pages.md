# Detail Pages

Use detail-page enrichment when list pages provide incomplete data.

For v3 schemas, there are two enrichment paths:

- `pipeline.calendar.parser.detailPage` (this page)
- `pipeline.event` with `event.type: "Html"`

Use one primary path per source to avoid duplicate detail fetches. See [Html Detail Enrichment Patterns](../v3/html-detail-enrichment.md).

## Enable Detail Pages When

- description is truncated
- image is missing on list page
- venue or address exists only on detail page

## Supported Fields

- detailPage.enabled
- detailPage.linkSelector
- detailPage.detailMappings

`detailMappings` supports the same keys as `mappings`, including `title`, `description`, `location`, `venueName`, `venueAddress`, `url`, `imageUrl`, `startDate`, `startTime`, `endDate`, `endTime`, and `recurrenceRule`.

Merge behavior:

- Listing-page values are parsed first.
- Detail-page values override listing values when non-empty.
- For split time extraction, detail `startDate` + `startTime` can replace a placeholder list `startTime`.

Rules:

- `detailPage.enabled` must be `true` to activate detail fetches.
- Runtime detail enrichment must also be enabled with environment variable `WHENEBER_HTMLLITE_DETAIL_ENRICHMENT_ENABLED`.
- If the environment variable is missing, detail enrichment defaults to enabled.
- `detailPage.linkSelector` must resolve to a detail URL from the list card scope.
- `detailMappings` selectors run against the detail-page DOM.

## Example

```json
{
  "detailPage": {
    "enabled": true,
    "linkSelector": "a@href",
    "detailMappings": {
      "title": "h1",
      "startDate": ".event__date",
      "startTime": ".event__time",
      "description": ".event-full-description",
      "imageUrl": "img.hero@src",
      "venueAddress": ".venue-address",
      "recurrenceRule": ".recurrence-info::attr(data-rrule)"
    }
  }
}
```

## Performing Arts and Theatre Sites

Performing arts, theatre, and symphony calendar list pages almost universally show only teaser content: a title, a promotional blurb, and a ticket link. Specific dates, times, venue details, and full descriptions live on individual performance detail pages.

Plan for `detailPage.enabled: true` from the start on these sources. Do not expect usable date or time data from the list page.

### When the list page has no date information at all

When the list page carries no date and all timing data lives on detail pages, you cannot satisfy the required `startTime` field from list-page extraction alone. Use a `literal:` sentinel value to pass validation and allow detail enrichment to run:

```json
{
  "mappings": {
    "title": "h2 a",
    "url": "h2 a::attr(href)",
    "startTime": "literal:2026-01-01T00:00:00-08:00"
  },
  "detailPage": {
    "enabled": true,
    "linkSelector": "a::attr(href)",
    "detailMappings": {
      "startDate": ".performance-date",
      "startTime": ".performance-time",
      "description": ".performance-description",
      "venueAddress": ".venue-address"
    }
  },
  "validation": {
    "requiredFields": ["title", "startTime", "url"]
  }
}
```

The `literal:` value passes the required-field check and allows the parse to proceed. The detail enrichment stage overwrites it with the real extracted date.

> **Important:** A `literal:` startTime is a bootstrap aid, not a contributor handoff value. Confirm that detail enrichment is running and that `validation.sampleEvents` show real extracted dates — not `2026-01-01` — before marking the schema ready.
