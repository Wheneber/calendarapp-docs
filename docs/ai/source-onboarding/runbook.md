# Autonomous Source Onboarding Runbook

Use this runbook for autonomous agents onboarding a new HtmlLite source with no human interaction.

---

## Execution Rules

- do not pause for manual review requests mid-run
- if one strategy fails, automatically try the next strategy
- stop only on terminal failure conditions
- emit a final machine-readable summary

## Before answering, read the following pages:

Read the docs under [HtmlLite](../../source-schemas/html-lite).

## Step 1: Gather Inputs

Required:
- sourceName
- feedUrl

Optional:
- expectedLocation
- expectedCategory
- expectedRegion
- expectedLanguage
- knownEventCardSelector
- knownTimeSelector
- knownNextLinkSelector

## Step 2: Detect Viability

1. Fetch feedUrl HTML.
2. Confirm event cards exist in static HTML.
3. If not, detect JS-rendered indicators.
4. If JS-rendered, search for JSON API or ICS alternatives.

Terminal failure:
- no static event HTML and no discoverable API or ICS alternative

## Step 3: Build Initial Schema

Minimum:
- eventCardSelector
- mappings.title
- mappings.startTime
- validation.requiredFields = ["title", "startTime"]

Bootstrap strategy:

1. Start with minimal schema and validate quickly.
2. If zero events, simplify `eventCardSelector` to the most stable repeated title wrapper.
3. If list-page time is not reliably parseable, use temporary `literal:` startTime.
4. Move real time extraction to detail mappings using `startDate` + `startTime`.
5. Only then add pagination breadth and richer fields.

Mapping DSL rule:
- mappings values must be strings only.
- HtmlLite supports simple CSS-like selectors and XPath.
- prefer simple CSS-like selectors for tag/class/id and descendant-by-space patterns, for example: .event-title or h2 a
- use `xpath=` when you need unsupported CSS features like `>`, `.a, .b`, sibling logic, or positional filters
- text/datetime fields use selector strings, for example: .event-title
- attribute fields use selector@attribute, for example: a@href
- do not use unsupported CSS operators directly; rewrite those selectors as XPath instead
- if the page splits date and time, map `startDate` and `startTime`; `startTime` may be a time-only value or a visible range
- do not emit object-style mappings with selector/type/attribute keys

Detail-page rule:

- when list fields are incomplete, set `detailPage.enabled = true`
- ensure `detailPage.linkSelector` resolves to a URL from list-card scope
- run `detailMappings` against detail DOM and let these override list values

Identity requirement:
1. explicit ID attribute
2. detail URL
3. permalink or slug
4. stable fallback

## Step 4: Choose Pagination Mode

Decision order:
1. nextLink
2. queryIncrement
3. pathIncrement
4. fixedUrls
5. single page with pagination risk note

Pagination contract:
- Use `pagination.type` (not `pagination.mode`).
- Allowed values: `nextLink`, `queryIncrement`, `pathIncrement`, `fixedUrls`.
- `query` is invalid. For query-based paging, use `queryIncrement` with `parameter`, `start`, and `step`.

## Step 5: Add Detail Enrichment If Needed

Enable detailPage when list pages are insufficient.

## Step 6: Submit Draft And Evaluate Validation

1. POST /api/source-schemas
2. Read response.validation
3. If validation.isSuccess is false, adjust selectors/pagination and resubmit

Adjustment priority when validation.totalEventsParsed = 0:

1. simplify eventCardSelector
2. verify required field mappings (`title`, `startTime`)
3. use `literal:` startTime for bootstrap if needed
4. verify linkSelector and detail mappings
5. add pagination only after non-zero parse is achieved

Max attempts:
- 8 schema refinement submissions

Terminal failure:
- no successful validation after max attempts

## Step 7: Handoff To Admin Review

Produce admin handoff packet with:
- schemaId
- feedUrl
- final schemaDefinition
- validation.totalEventsParsed
- validation.sampleEvents summary
- validation.isSuccess

## Step 8: Final Report

Output:
- sourceName
- feedUrl
- schemaId
- status
- paginationModeUsed
- detailEnrichmentUsed
- validationTotalEventsParsed
- validationIsSuccess
- handoffReady
- blockers
- nextActions

Success final report example:

```json
{
	"sourceName": "Example Events",
	"feedUrl": "https://www.example.org/events",
	"schemaId": "1acba9be-d70c-435a-8efb-123e350638e5",
	"status": "Success",
	"paginationModeUsed": "nextLink",
	"detailEnrichmentUsed": false,
	"validationTotalEventsParsed": 12,
	"validationIsSuccess": true,
	"handoffReady": true,
	"blockers": [],
	"nextActions": [
		"Send schemaId and validation summary to admin review queue"
	]
}
```

Failure final report example:

```json
{
	"sourceName": "Example Events",
	"feedUrl": "https://www.example.org/events",
	"schemaId": "7d88c4a3-c9d4-4c4d-beb1-b9d71e590fa3",
	"status": "Failed",
	"paginationModeUsed": "queryIncrement",
	"detailEnrichmentUsed": false,
	"validationTotalEventsParsed": 0,
	"validationIsSuccess": false,
	"handoffReady": false,
	"blockers": [
		"validation.errorMessage: No parser registered for schema type HtmlLite"
	],
	"nextActions": [
		"Adjust schemaDefinition and resubmit",
		"If failure persists after max attempts, escalate with payload and error details"
	]
}
```
