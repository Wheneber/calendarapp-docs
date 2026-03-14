# Validation Checklist

## Quality Gates Before Submission

Require all of the following:
- submit response validation.isSuccess is true
- submit response validation.totalEventsParsed is non-zero and reasonable
- submit response validation.sampleEvents title, startTime, and url values are valid
- event IDs are stable and not title-derived
- time values are parseable and timezone assumptions are sane
- pagination is configured when multiple pages exist
- detail enrichment is enabled when list pages are incomplete

## Submission Checklist

- compatibility check completed
- schema submitted as Draft
- schema ID and validation notes handed off to admins

## Schema Quality Levels

| Level | Expected Fields |
|---|---|
| Basic | title + startTime |
| Standard | title + startTime + url + location |
| Rich | title + startTime + endTime + url + location + description |
| Premium | Rich + imageUrl + venueName + venueAddress |
