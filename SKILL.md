---
name: horoscope-api
description: |
  Look up daily, weekly, monthly, or yearly horoscope readings for any of the
  12 Western sun signs. The user provides their sun sign and desired time range;
  the skill returns the current horoscope text, date range, and sign.
  Wraps the open-source Horoscope-API by Tapasweni Pathak (Python / Flask,
  scraping GaneshaSpeaks).
---

# Horoscope Lookup by Sun Sign

## Provenance

- **Original repo**: <https://github.com/tapaswenipathak/Horoscope-API>
- **Discovery URL**: <https://x.com/tapaswenipathak>
- **License**: MIT
- **Language / stack**: Python, Flask, lxml, requests
- **Data source**: GaneshaSpeaks (ganeshaspeaks.com) — scraped server-side

This skill was shaped from the original repository, which provides a REST API
but does not contain a SKILL.md.

## User Job

A user wants to quickly read their horoscope for today, this week, this month,
or this year — without visiting ad-heavy astrology sites or setting up any API
themselves. They provide their Western sun sign (Aries through Pisces) and pick
a time range; the skill returns the horoscope reading.

## Inputs

| Input | Required | Description |
|---|---|---|
| `sunsign` | yes | One of the 12 Western sun signs: Aries, Taurus, Gemini, Cancer, Leo, Virgo, Libra, Scorpio, Sagittarius, Capricorn, Aquarius, Pisces |
| `period` | no | `today` (default), `week`, `month`, or `year` |

## Output Contract

Return a JSON object:

```json
{
  "sunsign": "libra",
  "period": "today",
  "date": "2026-06-21",
  "horoscope": "The full horoscope text for the requested period."
}
```

- `date` is a date string for daily, a date range for weekly, a month label for
  monthly, or a year label for yearly.
- `horoscope` is the full reading text, cleaned of HTML artifacts and ad copy.

## Workflow

1. Accept the user's sun sign (case-insensitive) and optional period. Default
   period to `today` if not specified.
2. Validate the sun sign against the 12 Western zodiac signs. Reject unknown
   signs with a clear error.
3. Fetch the horoscope from GaneshaSpeaks via the endpoint pattern:
   - Daily: `https://www.ganeshaspeaks.com/horoscopes/daily-horoscope/{sunsign}`
   - Weekly: `https://www.ganeshaspeaks.com/horoscopes/weekly-horoscope/{sunsign}`
   - Monthly: `https://www.ganeshaspeaks.com/horoscopes/monthly-horoscope/{sunsign}`
   - Yearly: `https://www.ganeshaspeaks.com/horoscopes/yearly-horoscope/{sunsign}`
4. Parse the HTML response using XPath to extract the date/period label and the
   horoscope body text from the `#daily` container.
5. Clean the extracted text: strip leading/trailing whitespace, remove embedded
   ad copy and promotional upsell paragraphs (e.g. "Get It Now!" blocks),
   normalize whitespace.
6. Return the structured output per the contract above.

## Supported Sun Signs

Aries, Taurus, Gemini, Cancer, Leo, Virgo, Libra, Scorpio, Sagittarius,
Capricorn, Aquarius, Pisces.

## Limitations

- Data is scraped from a third-party site; availability depends on
  GaneshaSpeaks uptime and page structure stability.
- Horoscope content is entertainment-grade astrology, not a professional
  service.
- The original Heroku deployment is no longer live; this skill wraps the
  scraping logic directly.
