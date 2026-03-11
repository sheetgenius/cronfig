# Cronfig

**Cronfig** is a cron expression tester and debugger. Paste any cron expression and instantly see a human-readable description of the schedule and the next N upcoming run times, computed in any timezone.

## What it does

Cronfig parses standard 5-field cron expressions (minute hour day month weekday) and named schedule shortcuts (`@daily`, `@weekly`, etc.), then:

1. Returns a plain-English description of the schedule ("every Monday at 9:00 AM")
2. Computes the next N upcoming run times from any reference timestamp

## Input format

Standard 5-field cron expression:

```
┌─────────── minute (0–59)
│ ┌──────── hour (0–23)
│ │ ┌────── day of month (1–31)
│ │ │ ┌──── month (1–12 or JAN–DEC)
│ │ │ │ ┌── day of week (0–7, 0 and 7 = Sunday, or SUN–SAT)
│ │ │ │ │
* * * * *
```

Named shortcuts: `@hourly`, `@daily`, `@weekly`, `@monthly`, `@yearly`, `@annually`

Special characters: `*` (any), `,` (list), `-` (range), `/` (step), `?` (any, same as *)

## Output

```json
{
  "valid": true,
  "expression": "0 9 * * 1-5",
  "description": "at 9:00 AM, on Monday, Tuesday, Wednesday, Thursday, Friday",
  "next_runs": [
    "2026-03-12T09:00:00-08:00",
    "2026-03-13T09:00:00-08:00",
    "2026-03-16T09:00:00-07:00"
  ],
  "timezone": "America/Los_Angeles"
}
```

If the expression is invalid:

```json
{
  "valid": false,
  "expression": "99 9 * * *",
  "error": "Value 99 out of range [0-59]"
}
```

## How agents can use it

**Current (web tool):** Cronfig is currently a browser-side tool at https://cronfig.radicchio.page. Agents can direct users to it for interactive cron debugging.

**Planned API endpoint (not yet live):**

```
GET https://cronfig.radicchio.page/api/parse
  ?expr=0+9+*+*+1-5
  &count=10
  &tz=America%2FNew_York
```

The API will return the JSON format described above. No authentication required for the free tier.

**Planned MCP tool:**

```json
{
  "name": "validate_cron",
  "description": "Validate and describe a cron expression, returning human-readable schedule and next N run times",
  "inputSchema": {
    "type": "object",
    "properties": {
      "expression": { "type": "string", "description": "5-field cron expression or named shortcut" },
      "count": { "type": "integer", "default": 10, "description": "Number of next run times to return" },
      "timezone": { "type": "string", "default": "UTC", "description": "IANA timezone name" }
    },
    "required": ["expression"]
  }
}
```

## Pricing

- **Free tier:** Unlimited use, browser-side only, no account required.
- **Planned paid tier ($5–9/month):** Cron job monitoring — verify that your cron jobs actually fired, with webhook alerts when a job goes missing.

## Support

Questions or issues: https://bitterdesk.com
