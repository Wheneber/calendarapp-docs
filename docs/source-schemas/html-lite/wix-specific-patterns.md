# Wix-Specific Selector Patterns

## Why Wix is Different

Wix uses a component ID system where each reusable element has a stable ID prefix. For example:
- `comp-mcsmgbbs__` → repeated event card component
- `comp-mcsn738v__` → different event title within each card
- `comp-jfx5gg4w__` → shared location element

**Key insight:** CSS selector `[id^="prefix"]` syntax doesn't translate to XPath. You must use XPath `starts-with()` function.

## Essential XPath Patterns for Wix

### Pattern 1: Match element with ID starting with prefix

**CSS (won't work directly):**
```css
[id^="comp-mcsmgbbs__"]
```

**XPath (correct):**
```xpath
.//*[starts-with(@id, "comp-mcsmgbbs__")]
```

Use this for the event card selector. Example:
```json
"eventCardSelector": ".//*[starts-with(@id, \"comp-mcsmgbbs__\")]"
```

### Pattern 2: Get text from specific sub-element within card

Once you've selected a card, navigate to child elements:

```json
"mappings": {
  "title": ".//*[starts-with(@id, \"comp-mcsn738v__\")]//span",
  "startTime": ".//*[starts-with(@id, \"comp-jfx5gg4w__\")]/text()"
}
```

### Pattern 3: Extract href from link within card

```json
"mappings": {
  "url": ".//a[1]/@href"
}
```

## Common Wix Event Calendar Structure

Typical HTML structure for a Wix event list:

```html
<div id="comp-mcsmgbbs__item-abc123">
  <!-- title -->
  <div id="comp-mcsn738v__item-abc123">
    <span>Event Title</span>
  </div>
  
  <!-- date/time -->
  <div id="comp-jfx5gg4w__item-abc123">
    May 24, 2026 at 7:00pm
  </div>
  
  <!-- link -->
  <a href="/event-detail?id=abc123">View Details</a>
</div>

<div id="comp-mcsmgbbs__item-xyz789">
  <!-- repeats... -->
</div>
```

**eventCardSelector:** `.//*[starts-with(@id, "comp-mcsmgbbs__")]`

**Mappings:**
- `title`: `.//*[starts-with(@id, "comp-mcsn738v__")]//span`
- `startTime`: `.//*[starts-with(@id, "comp-jfx5gg4w__")]/text()`
- `url`: `.//a/@href`

## How to Find Your Component IDs

1. Open source page in browser
2. Right-click on the event title → Inspect
3. Look for the nearest parent `<div>` with `id="comp-..."` 
4. Note the 8-character prefix after `comp-`
5. Use in XPath with `starts-with(@id, "prefix")`

## Common Mistakes

| Mistake | Error | Fix |
|---------|-------|-----|
| Using CSS selector `[id^="prefix"]` | "Invalid token" in parser | Use XPath: `.//*[starts-with(@id, "prefix")]` |
| Selector matches too broadly | Extra events beyond number on page | Add more specificity with `and` predicates or child element filtering |
| .text() returns array instead of single value | Null in mapping | Use `//text()[1]` to get first text node or wrap in span selector |
| Date text contains extra whitespace | Parse failure | Add `trim` transform: `"startTime": "...",transforms": ["trim"]` |

## Example: Metropolitan Performing Arts (Real Case)

This schema worked for a Wix-based performing arts calendar:

```json
{
  "eventCardSelector": ".//*[starts-with(@id, \"comp-mcsmgbbs__\")]",
  "mappings": {
    "title": ".//*[starts-with(@id, \"comp-mcsn738v__\")]//span",
    "startTime": ".//*[starts-with(@id, \"comp-jfx5gg4w__\")]/text()",
    "url": ".//a[1]/@href"
  },
  "validation": {
    "requiredFields": ["title", "startTime"]
  }
}
```

**Result:** 3 of 14 events parsed (would be higher if date range "May 8-24" format was supported—see [time-handling.md](time-handling.md#date-range-parsing--silent-failures))

## Next Steps

1. **Test-fetch:** Verify parse count matches visible events
2. **Check date formats:** Does source use date ranges? If yes, see time-handling.md for solutions
3. **Add detail page:** If important fields are missing from list, enable detail-page enrichment
4. **Compare samples:** Review `sampleEvents` from test-fetch response against real events on page
