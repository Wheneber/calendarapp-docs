# Detail Pages

Use detail-page enrichment when list pages provide incomplete data.

## Enable Detail Pages When

- description is truncated
- image is missing on list page
- venue or address exists only on detail page

## Supported Fields

- detailPage.enabled
- detailPage.linkSelector
- detailPage.detailMappings

`detailMappings` supports the same keys as `mappings`: `title`, `description`, `location`, `venueName`, `venueAddress`, `url`, `imageUrl`, `endTime`, `recurrenceRule`. Detail values override list-page values when non-empty.

## Example

```json
{
  "detailPage": {
    "enabled": true,
    "linkSelector": "a::attr(href)",
    "detailMappings": {
      "description": ".event-full-description",
      "imageUrl": "img.hero::attr(src)",
      "venueAddress": ".venue-address",
      "recurrenceRule": ".recurrence-info::attr(data-rrule)"
    }
  }
}
```
