# Troubleshooting

## Invalid request payload

Usually means schemaDefinition was sent as an object instead of a JSON string.

## validation.totalEventsParsed = 0

- verify eventCardSelector first
- verify startTime extraction and date format
- prefer XPath for complex nested nodes
- confirm source is static HTML and not JS-only

## validation.isSuccess = false

- inspect validation.errorMessage in submit response
- correct schemaDefinition and resubmit Draft

## Expression must evaluate to a node-set

XPath is returning a scalar where a node query is required. Move attribute access into ::attr(...) after selecting a node.
