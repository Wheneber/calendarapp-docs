# ChatGPT Schema Generation Prompt

Copy the template below, replace `<FEED_URL>` with your calendar feed URL, and paste into ChatGPT. The AI will read the schema documentation, generate a valid schema, test it, and submit it for you.

> **For AI agents with HTTP tool use** (custom GPTs with API actions, etc.): use the [Autonomous Runbook](runbook.md) instead.

---

## Simple User Template

Just copy, change the URL, and paste:

```
Generate a calendar schema for this feed:
- Feed URL: <FEED_URL>

Steps:
1. First, read the schema index and guidelines: https://martinisaksen.github.io/calendarapp-docs/llms.txt
2. From that index, read: choose-source-type.md and submission-api-and-validation.md
3. Browse <FEED_URL> to determine the source type (Ics, Rss, JsonApi, or HtmlLite)
4. Generate the complete schema including name, description, feedUrl, schemaDefinition, and metadata
5. Test the schema by POSTing to http://localhost:5047/api/source-schemas/test-fetch
6. If successful (eventCount > 0), submit the exact same payload to http://localhost:5047/api/source-schemas/community-submissions
7. Print the schema ID and event count

For Ics sources: schemaDefinition must only contain `validation` or be `{}`. Do not add url, mappings, transforms, or sourceType.

Output:
1. The source type chosen (Ics/Rss/JsonApi/HtmlLite)
2. Brief evidence (why this type)
3. The complete JSON payload (name, description, type, feedUrl, schemaDefinition, metadata)
4. PowerShell commands for both test and submit (if capable)
5. After testing: "Schema ID: [id], Events parsed: [count], Status: [success/error]"

Use the same payload for both test and submit — no changes needed between them.
```

---

## How It Works

When you paste this template with your URL:

1. **AI reads the index** — The `llms.txt` file contains all raw GitHub URLs to schema documentation (plain text, no JavaScript)
2. **AI inspects your feed** — It determines whether the source is Ics, Rss, JsonApi, or HtmlLite
3. **AI generates schema** — It builds the smallest valid schema for that type
4. **AI tests the schema** — It calls `/api/source-schemas/test-fetch` to validate the schema and event extraction
5. **AI submits** — If test succeeds (>0 events), it calls `/api/source-schemas/community-submissions` to submit for admin review
6. **AI prints results** — Schema ID, event count, and any errors

The output is ready to run—no edits needed.

---

## Quick Reference: What Goes in the Prompt

| Field | What to Enter |
|-------|---------------|
| `<FEED_URL>` | The URL of the calendar feed (ICS file, RSS feed, JSON endpoint, or HTML page) |

That's it. The AI handles the rest.

---

## Troubleshooting

**AI doesn't read llms.txt:**
- Explicitly tell it: "First, read https://martinisaksen.github.io/calendarapp-docs/llms.txt"
- Then ask it to generate the schema

**AI generates unsupported fields:**
- Copy the error message from test-fetch back into the chat
- Ask the AI: "Fix this error in the schema and resubmit"

**Test returns 0 events:**
- Share the error message with the AI
- Ask: "What should we debug? Should we try a different source type?"

---

## After Submission

The AI will print:
- `sourceSchema.id` — The ID of your submitted schema (save this for reference)
- `validation.isSuccess: true` — Schema passed validation
- `validation.totalEventsParsed` — Number of events extracted from the feed

If `isSuccess: false`, an error message explains what to fix. Copy it back to the AI and ask for a corrected schema.

---

## Tips for Success

- **Keep the URL in the prompt.** Change only the feed URL; leave everything else.
- **If the AI breaks the rules,** share the error message and ask it to fix the schema.
- **If it generates 0 events,** ask the AI what debugging step it recommends (source type, selector, timezone, etc.).
- **Save the schema ID** after successful submission — you'll need it if you want to update the schema later.
- **Test with a simple source first** (like a public ICS calendar) to verify your setup before trying complex JSON or HTML sources.


