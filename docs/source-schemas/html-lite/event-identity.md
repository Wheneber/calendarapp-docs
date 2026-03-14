# Event Identity

Stable external IDs are required for reliable deduplication and update behavior.

## Priority Order

1. explicit event ID attribute
2. event detail page URL
3. slug or permalink
4. generated fallback

## Examples

Good:
- id: a::attr(href)

Acceptable:
- id: xpath=.//a/@href

Avoid:
- ID derived from visible title text alone

## Field Behavior

External ID fallback order at runtime:
1. mappings.id
2. resolved event URL
3. generated fallback ID
