# Source Schema JSON Schema Files

CalendarApp publishes contributor-facing JSON Schema files for each supported `schemaDefinition` type.

Use these files to help humans and AI generate more accurate schemas before calling the API.

## Available Files

| Type | Rendered link | Raw GitHub link (for AI / direct fetch) |
|---|---|---|
| `Ics` | [ics.schema.json](ics.schema.json) | [raw](https://raw.githubusercontent.com/martinisaksen/calendarapp-docs/main/docs/source-schemas/ics.schema.json) |
| `Rss` | [rss.schema.json](rss.schema.json) | [raw](https://raw.githubusercontent.com/martinisaksen/calendarapp-docs/main/docs/source-schemas/rss.schema.json) |
| `JsonApi` | [json-api-v2.schema.json](json-api-v2.schema.json) | [raw](https://raw.githubusercontent.com/martinisaksen/calendarapp-docs/main/docs/source-schemas/json-api-v2.schema.json) |
| `HtmlLite` | [html-lite.schema.json](html-lite.schema.json) | [raw](https://raw.githubusercontent.com/martinisaksen/calendarapp-docs/main/docs/source-schemas/html-lite.schema.json) |

Use the **raw** links when programmatically loading schemas into an AI tool or validation pipeline — they return plain JSON without HTML or JavaScript.

## Which File To Use

- Use [Ics JSON Schema](ics.schema.json) when `type = Ics`.
- Use [Rss JSON Schema](rss.schema.json) when `type = Rss`.
- Use [JsonApi V2 JSON Schema](json-api-v2.schema.json) when `type = JsonApi` and you are authoring a new source.
- Use [HtmlLite JSON Schema](html-lite.schema.json) when `type = HtmlLite`.

Do not reuse the JsonApi schema file for other source types. Each type has a different `schemaDefinition` shape.

## Important Scope Note

These files validate the inner `schemaDefinition` object only.

They do not validate the full outer API request body that contains fields such as:

- `name`
- `description`
- `type`
- `feedUrl`
- `schemaDefinition`
- `metadata`

They also do not replace backend validation.

Backend validation remains authoritative for:

- network and host restrictions
- some cross-field rules and runtime constraints
- parser-specific behavior
- final validation and test-fetch outcomes

Use these schema files as authoring aids, then confirm the result with `POST /api/source-schemas/test-fetch`.