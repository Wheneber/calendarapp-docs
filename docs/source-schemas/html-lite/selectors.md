# Selectors

## Discovery Workflow

1. Identify the repeating event card container.
2. Map title, time, url first.
3. Add description, location, image after submit validation succeeds.

Good card candidates:
- div.views-row
- li.event-card
- article.event
- div.event-teaser

## Supported Selector Patterns

- CSS-like selection:
  - tag, class, id, descendant by space
- XPath:
  - prefix with xpath=
- Attribute extraction:
  - selector::attr(name)
  - selector@attrName
- Literal values:
  - literal:Some fixed value

Examples:
- div.event-card
- .event-title a
- xpath=.//time[@datetime]
- a::attr(href)
- img@src

## Selector Scope Rules

- `eventCardSelector` runs against the listing page document.
- `mappings` selectors run relative to each matched event card node.
- `detailPage.linkSelector` runs relative to the matched event card node.
- `detailPage.detailMappings` selectors run against the fetched detail page document.

Scope example:

- If `eventCardSelector` is `h2.event__title` then mapping `title: "a"` selects the anchor inside that `<h2>`.
- In `detailMappings`, `title: "h1"` selects from the detail page root, not the list page card.

## CSS-like vs XPath

HtmlLite is not XPath-only. It supports a limited CSS-like subset and raw XPath.

Use XPath when:
- you need child combinators like `>`
- you need selector lists like `.a, .b`
- you need sibling logic, nth/first filters, or attribute predicates beyond the simple subset
- you need conditional logic or explicit node tests

XPath examples:
- xpath=.//time[@datetime]
- xpath=.//img[1]
- xpath=.//h2[@class='event-title']/a

XPath selectors with attribute predicates (for example `[@class='...']`) are supported.

Use CSS-like selectors when:
- matching simple class/id/tag structures
- using descendant-by-space selectors like `h2 a`
- extracting direct attributes with ::attr(...)

CSS-like examples:
- img::attr(src)
- a::attr(href)
- h2.event__title a

Avoid unsupported CSS syntax in HtmlLite:

- `>` child combinator
- selector lists separated by commas
- pseudo-selectors like `:first-child`
- adjacent and general sibling operators

If you need any of those, switch that selector to `xpath=` instead of trying to force full CSS syntax.

## CSS-to-XPath Quick Reference

**Why CSS attribute selectors fail:** HtmlLite converts selectors to XPath for DOM traversal. CSS attribute selectors like `[id^="prefix"]` produce invalid XPath syntax. Use `starts-with()` instead.

| CSS Selector | XPath | When To Use |
|---|---|---|
| `[id^="prefix"]` | `.//*[starts-with(@id, "prefix")]` | Match elements by ID prefix (Wix components, dynamic IDs) |
| `[class*="event"]` | `.//*[contains(@class, "event")]` | Match elements by partial class name |
| `.class-name` | `.//*[@class="class-name"]` or `.//*[contains(@class, "class-name")]` | Match by exact class or class presence |
| `element > child` | `element/child` | Direct child only (one level down) |
| `element descendant` | `element//descendant` | Any-depth descendant |
| `a[href$=".html"]` | `a[ends-with(@href, ".html")]` | Attribute value ends with |
| `a[href*="search"]` | `a[contains(@href, "search")]` | Attribute value contains |

## Wix-Specific Selector Patterns

Wix pages do not all expose the same stable selector strategy.

Prefer this order:
1. semantic `data-hook` selectors on Wix Events pages
2. machine-readable attributes when available
3. repeatable `comp-*` component prefixes on Wix CMS repeater pages

Wix Events pages often expose server-rendered hooks such as `events-card`, `title`, `date`, and `location`:

```json
{
  "eventCardSelector": "[data-hook='events-card']",
  "mappings": {
    "title": "a[data-hook='title']",
    "startTime": "[data-hook='date']",
    "location": "[data-hook='location']",
    "url": "a[data-hook='title']@href"
  }
}
```

When those hooks are missing, Wix CMS sites (built with Wix Editor) often use repeatable component ID patterns:

**Repeater Items:**
```xpath
.//*[starts-with(@id, "comp-mcsmgbbs__")]   // Match any element with ID prefix
.//*[starts-with(@id, "comp-")]//h2         // Get h2 inside Wix component
```

**Common Wix Component IDs:**
- `comp-mcsmgbbs__` - Rich text / title elements
- `comp-mcsn738v__` - Date / schedule elements
- `comp-md8it4c0__` - Button / link elements
- `comp-mcsmgbbu1__` - Description / paragraph elements

**Why prefixes work:**
Wix repeater components use stable ID prefixes (e.g., `comp-mcsmgbbs__`) that don't change between page reloads. The suffix (UUID) is unique per repeater item. Using `starts-with()` on the prefix reliably matches all event cards.

**Fallback Wix CMS Schema:**
```json
{
  "eventCardSelector": ".wixui-repeater__item",
  "mappings": {
    "title": ".//*[starts-with(@id, \"comp-mcsmgbbs__\")]//h2",
    "startTime": ".//*[starts-with(@id, \"comp-mcsn738v__\")]//h4",
    "description": ".//*[starts-with(@id, \"comp-mcsmgbbu1__\")]//p",
    "url": ".//*[starts-with(@id, \"comp-md8it4c0__\")]//a/@href"
  }
}
```

For a fuller Wix decision tree and fallback guidance, see [wix-specific-patterns.md](wix-specific-patterns.md).

Unsupported mapping syntax:

- `regex:(...)` is not a supported selector or mapping DSL in HtmlLite

If a date/time value needs normalization beyond selector extraction:

1. prefer extracting machine-readable attributes first (for example `time::attr(datetime)`)
2. use `startDate` plus `startTime` when the page splits date/time
3. if the source requires heavy transformation, consider `JsonApi` instead of HtmlLite

## Invalid vs Valid Examples

Chat models often try to hedge with multiple fallback selectors in a single string. That does not work in HtmlLite.

Invalid:

```json
{
  "eventCardSelector": ".event-listing, .events-listing-item, .event-item",
  "mappings": {
    "title": ".event-title, h3, h2"
  }
}
```

Why invalid:
- selector lists separated by commas are unsupported

Valid:

```json
{
  "eventCardSelector": "xpath=.//*[contains(@class,'event-listing') or contains(@class,'events-listing-item') or contains(@class,'event-item')]",
  "mappings": {
    "title": "xpath=.//*[contains(@class,'event-title') or self::h3 or self::h2][1]"
  }
}
```

Also avoid invented selectors that are not proven by the page source. A weaker but evidenced selector is better than a broader guessed one.

## Compound Class Selectors on WordPress and Page-Builder Sites

Some WordPress themes (including BeTheme with Muffin Builder, Divi, and similar builders) generate elements with multiple compound classes, for example `<div class="mcb-wrap three-fifth">`. Treating this as a CSS multi-class selector like `div.mcb-wrap.three-fifth` is not reliably supported in HtmlLite.

Use XPath `contains(@class, '...')` predicates instead:

```json
"eventCardSelector": "xpath=.//div[contains(@class,'mcb-wrap') and contains(@class,'three-fifth')]"
```

To avoid matching non-event blocks on the page, add a **child-presence guard** — an inner predicate that requires a known child element to exist:

```json
"eventCardSelector": "xpath=.//div[contains(@class,'mcb-wrap') and contains(@class,'three-fifth') and .//h2[@class='title']]"
```

This ensures only divs containing a title heading are matched as event cards.
