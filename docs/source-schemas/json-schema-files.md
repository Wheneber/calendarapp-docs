# Source Schema JSON Schema Files

CalendarApp publishes contributor-facing JSON Schema files for each supported `schemaDefinition` type.

Use these files to help humans and AI generate more accurate schemas before calling the API.

## Available Files

- [Ics JSON Schema](ics.schema.json)
- [Rss JSON Schema](rss.schema.json)
- [JsonApi V2 JSON Schema](json-api-v2.schema.json)
- [HtmlLite JSON Schema](html-lite.schema.json)

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