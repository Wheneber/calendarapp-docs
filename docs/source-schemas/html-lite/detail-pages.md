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

## Example

```json
{
  "detailPage": {
    "enabled": true,
    "linkSelector": "a::attr(href)",
    "detailMappings": {
      "description": ".event-full-description",
      "imageUrl": "img.hero::attr(src)",
      "venueAddress": ".venue-address"
    }
  }
}
```
