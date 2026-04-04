# Validation Checklist

## Early Validation (Before Final Submission)

Run these checks early to catch issues before submitting the schema. This saves iteration cycles.

### Selector Validation

1. **Test eventCardSelector in isolation**
   - Run `test-fetch` with only `eventCardSelector` and required fields (`title`, `startTime`)
   - Verify `totalEventsParsed` is non-zero and reasonable (matches count on source page)
   - If zero, use browser console to verify selector works: `document.querySelectorAll('.your-selector')`

2. **Check for date format compatibility**
   - In `sampleEvents`, verify all `startTime` values parse cleanly
   - Common failures:
     - Date ranges (e.g., "May 8 - 24, 2026") → Events silently dropped
     - Non-standard formats ("TBD", "Ongoing") → Silent drop
   - If low parse count, check if source uses date ranges (see [time-handling.md](time-handling.md) for workarounds)

3. **Validate mapping selectors**
   - Spot-check 3-5 sample events against source page
   - Verify title, date, URL are correctly extracted
   - If any field is null/empty in samples, fix selector before resubmitting

### Pre-Submission Quick Checklist

- [ ] Run `test-fetch` and check `totalEventsParsed` is non-zero
- [ ] Review `sampleEvents` array from test-fetch (not just count)
- [ ] Compare samples against 3+ visible events on source page
- [ ] Verify date formats are single dates (not ranges)
- [ ] Confirm selectors use XPath (not CSS like `[id^="..."]`)
- [ ] Check required fields have values in samples (title, startTime)

## Quality Gates Before Submission

Require all of the following:
- submit response validation.isSuccess is true
- submit response validation.totalEventsParsed is non-zero and reasonable
- submit response validation.sampleEvents title, startTime, and url values are valid
- event IDs are stable and not title-derived
- time values are parseable and timezone assumptions are sane
- pagination is configured when multiple pages exist
- detail enrichment is enabled when list pages are incomplete

Field completeness gate:
- compare parsed sample events against at least 3 known detail URLs
- ensure expected detail fields are present when the source page provides them

Recommended additional checks:
- sample events include expected city/region context
- URLs resolve to canonical event pages (not list-only links)
- detailPage.linkSelector resolves within the card scope
- runtime flag `CALENDARAPP_HTMLLITE_DETAIL_ENRICHMENT_ENABLED` is enabled when detail mappings are expected to populate fields
- when `fieldTransforms` are present, each transform is deterministic and low-risk (`trim`, `collapseWhitespace`, `removePrefix`, `removeSuffix`, `replaceLiteral`, `stripWrappingQuotes`, `nullIfEqualsAny`)
- high-risk transform patterns are not used (regex/expression execution, multi-field semantic reconstruction)

## Geolocation Quality Gate

Before handoff, verify one of these is true:

1. Source provides valid coordinates and sampled values are in valid latitude/longitude ranges.
2. Source does not reliably provide valid coordinates, but mapped `location` and `venueAddress` values are complete enough for geocoding fallback.

Checklist:

- no synthetic coordinates introduced by contributor or AI
- coordinate order verified when arrays are used
- `location` and `venueAddress` quality checked on at least 3 sample events when fallback is expected
- geolocation behavior noted in handoff summary as `source-coordinates-used` or `geocode-fallback-expected`

## Submission Checklist

- compatibility check completed
- schema submitted as Draft
- schema ID and validation notes handed off to admins

For autonomous execution, also require:
- final machine-readable report emitted
- blockers and nextActions populated when status is Failed
- retry attempts are bounded and documented

## Schema Quality Levels

| Level | Expected Fields |
|---|---|
| Basic | title + startTime |
| Standard | title + startTime + url + location |
| Rich | title + startTime + endTime + url + location + description |
| Premium | Rich + imageUrl + venueName + venueAddress |

## Minimum Acceptance Criteria

Approve handoff readiness only when:
1. `validation.isSuccess` is true
2. `validation.totalEventsParsed` is greater than zero
3. sampled events look semantically correct for title/time/url
4. ID and time handling rules are stable across at least one pagination step (if paging exists)

## Field Completeness Matrix (Recommended)

Use this quick matrix against known detail URLs before handoff:

| Field | Expected On Source | Present In Parsed Sample |
|---|---|---|
| title | yes/no | yes/no |
| startTime | yes/no | yes/no |
| endTime | yes/no | yes/no |
| description | yes/no | yes/no |
| location | yes/no | yes/no |
| venueName | yes/no | yes/no |
| venueAddress | yes/no | yes/no |
| imageUrl | yes/no | yes/no |
| url | yes/no | yes/no |

If parse count is non-zero but expected fields are still missing, treat as a quality failure and continue refinement.
