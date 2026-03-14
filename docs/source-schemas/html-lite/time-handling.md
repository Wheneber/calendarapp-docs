# Time Handling

Time parsing is a common failure point.

## Preferred Extraction Order

1. time datetime attribute
2. schema.org or meta startDate
3. data-start or similar data attributes
4. visible text parsing

Examples:
- Preferred: xpath=.//time[@datetime]::attr(datetime)
- Riskier: .event-time
- Risky: visible text like 10:30 am - 11:30 am

## Validation Tips

- verify submit response validation.sampleEvents have parseable datetime values
- verify timezone assumptions
- verify AM/PM and all-day handling

## Default Behavior

- EndTime defaults to StartTime + 1 hour when no endTime mapping is provided
- IsAllDay defaults to false
