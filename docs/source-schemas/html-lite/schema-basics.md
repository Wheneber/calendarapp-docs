# Schema Basics

## Minimal Required Fields

- eventCardSelector
- mappings.title
- mappings.startTime
- validation.requiredFields includes title and startTime

## Minimal Example

```json
{
  "eventCardSelector": "div.event-teaser",
  "mappings": {
    "title": "h3",
    "startTime": "xpath=.//time[@datetime]::attr(datetime)",
    "url": "a::attr(href)"
  },
  "validation": {
    "requiredFields": ["title", "startTime"]
  }
}
```

## Full Example

```json
{
  "eventCardSelector": "div.event-teaser",
  "mappings": {
    "id": "a::attr(href)",
    "title": "h3",
    "startTime": "xpath=.//time[@datetime]::attr(datetime)",
    "description": ".event-teaser__description",
    "location": ".event-teaser__address",
    "venueName": "literal:My Venue",
    "venueAddress": ".event-teaser__address",
    "url": "a::attr(href)",
    "imageUrl": "img::attr(src)"
  },
  "detailPage": {
    "enabled": false
  },
  "pagination": {
    "type": "nextLink",
    "nextPageSelector": ".pager-next a::attr(href)",
    "maxPages": 10
  },
  "validation": {
    "requiredFields": ["title", "startTime"]
  }
}
```
