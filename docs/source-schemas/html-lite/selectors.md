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

## CSS-like vs XPath

Use XPath when:
- selecting nested nodes
- checking for attribute presence
- selecting first or nth element
- using conditional logic

XPath examples:
- xpath=.//time[@datetime]
- xpath=.//img[1]

Use CSS-like selectors when:
- matching simple class/id/tag structures
- extracting direct attributes with ::attr(...)

CSS-like examples:
- img::attr(src)
- a::attr(href)
