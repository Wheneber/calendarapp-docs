# Troubleshooting

## Invalid request payload

Usually means schemaDefinition was sent as an object instead of a JSON string.

## validation.totalEventsParsed = 0

- verify eventCardSelector first
- verify startTime extraction and date format
- prefer XPath for complex nested nodes
- confirm source is static HTML and not JS-only

If still zero, apply this fallback sequence:

1. Reduce `eventCardSelector` to a very stable repeated node (often title wrapper).
2. Use minimal mappings only: `title`, `url`, parseable `startTime`.
3. If list time text is unreliable, set temporary `startTime` to a `literal:` datetime.
4. Enable `detailPage` and map `startDate` + `startTime` there.
5. Add pagination after non-zero validation is achieved.

Common causes of false zero results:

- selector syntax uses unsupported CSS (`>`, `.a, .b`, pseudo-selectors)
- `linkSelector` does not produce a detail URL
- `startTime` cannot be parsed from list page
- mappings are objects instead of strings

## validation.isSuccess = false

- inspect validation.errorMessage in submit response
- correct schemaDefinition and resubmit Draft

## Expression must evaluate to a node-set

XPath is returning a scalar where a node query is required. Move attribute access into ::attr(...) after selecting a node.

If using HtmlLite custom attribute syntax, prefer `selector@attribute` or `selector::attr(attribute)`.
