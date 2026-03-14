# HtmlLite Source Submission Overview

Use HtmlLite when onboarding a new source that serves static HTML containing event cards.

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
