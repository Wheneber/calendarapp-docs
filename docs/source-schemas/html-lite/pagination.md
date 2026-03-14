# Pagination

Many sources span multiple pages.

## Compatibility Check

Look for patterns such as:
- ?page=
- ?p=
- /page/
- offset=

## Supported Modes

- nextLink with nextPageSelector
- queryIncrement with parameter, start, step
- pathIncrement with pathTemplate, start, step
- fixedUrls with urls
- maxPages for safety bounds

## Recommended Defaults

- start with nextLink when a reliable next-page element exists
- use queryIncrement or pathIncrement when URLs are predictable and no next link exists
- use fixedUrls for irregular page sets

## Example

```json
{
  "pagination": {
    "type": "queryIncrement",
    "parameter": "page",
    "start": 0,
    "step": 1,
    "maxPages": 10
  }
}
```
