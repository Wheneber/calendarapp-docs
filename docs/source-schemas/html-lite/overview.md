# HtmlLite Source Submission Overview

Use HtmlLite when onboarding a new source that serves static HTML containing event cards.

## HtmlLite Quick Reference

- `eventCardSelector`: selector for repeating event nodes
- `mappings` values: strings only
- Text extraction: `selector`
- Attribute extraction: `selector@attribute` or `selector::attr(attribute)`
- Constant value: `literal:...`
- Selector language: limited CSS-like selectors plus `xpath=` when needed
- `detailPage.linkSelector` must resolve to a URL
- `detailPage.detailMappings` run against detail-page DOM
- `pagination.type` allowed values: `nextLink`, `queryIncrement`, `pathIncrement`, `fixedUrls`
- `schemaDefinition` must be sent as a JSON string in API payloads

## AI-First Workflow From A Starting URL

Use this deterministic order to avoid 0-event failures:

1. Find one stable repeating card selector.
2. Map only `title`, `url`, and a parseable `startTime` first.
3. Validate with `POST /api/source-schemas`.
4. Add `id` and pagination.
5. Add `detailPage` enrichment for time/location/description/image if list fields are incomplete.
6. Re-validate after each change.

If list-page time is unreliable, use a temporary parseable `literal:` start time to keep cards from being dropped, then overwrite with detail-page `startDate` + `startTime` mappings.

## End-to-End Flow

1. Confirm source compatibility with static HTML parsing.
2. Build a minimal schema.
3. Add identity, pagination, and detail-page rules as needed.
4. Submit Draft and review backend validation result.
5. Handoff schema ID and validation notes for admin review.

## Endpoint Summary

- POST /api/source-schemas

## Request Contract Rule

schemaDefinition must be sent as a JSON string in request payloads.

## Submit Validation Result

The submit endpoint performs server-side fetch/parse validation and returns:
- sourceSchema: the created Draft source schema
- validation.isSuccess: pass/fail
- validation.totalEventsParsed: parsed event count
- validation.sampleEvents: sample parsed rows (up to 5)
- validation.errorMessage: failure reason when validation fails

Quick interpretation:
- Success: validation.isSuccess = true and validation.totalEventsParsed > 0
- Failure: validation.isSuccess = false and validation.errorMessage explains what to fix
