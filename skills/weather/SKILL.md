---
name: weather
description: "Get current weather and forecasts via Open-Meteo (primary) or wttr.in (fallback). Use when: user asks about weather, temperature, or forecasts for any location. NOT for: historical weather data, severe weather alerts, or detailed meteorological analysis. No API key needed."
homepage: https://open-meteo.com/
metadata: { "openclaw": { "emoji": "🌤️", "requires": { "bins": ["curl"] } } }
---

# Weather Skill

Get current weather conditions and forecasts using **Open-Meteo API** (primary) or wttr.in (fallback).

## Default Location

**User's Default**: 江西省南昌市南昌县  
**Coordinates**: Latitude 28.4439, Longitude 115.7539

## When to Use

✅ **USE this skill when:**

- "What's the weather?"
- "Will it rain today/tomorrow?"
- "Temperature in [city]"
- "Weather forecast for the week"
- Travel planning weather checks

## When NOT to Use

❌ **DON'T use this skill when:**

- Historical weather data → use weather archives/APIs
- Climate analysis or trends → use specialized data sources
- Hyper-local microclimate data → use local sensors
- Severe weather alerts → check official NWS sources
- Aviation/marine weather → use specialized services (METAR, etc.)

## API Endpoints

### Primary: Open-Meteo (Recommended)

**Forecast API**: `https://api.open-meteo.com/v1/forecast`  
**Geocoding**: `https://geocoding-api.open-meteo.com/v1/search`

### Fallback: wttr.in

**Base URL**: `https://wttr.in/`

## Commands

### Current Weather (Open-Meteo)

```bash
# Real-time weather for Nanchang County (default location)
curl "https://api.open-meteo.com/v1/forecast?latitude=28.4439&longitude=115.7539&current=temperature_2m,relative_humidity_2m,weather_code,wind_speed_10m,precipitation&timezone=auto"

# For any city (example: Beijing)
curl "https://api.open-meteo.com/v1/forecast?latitude=39.9042&longitude=116.4074&current=temperature_2m,weather_code,wind_speed_10m&timezone=auto"
```

### Weather Forecast (Open-Meteo)

```bash
# 7-day forecast for Nanchang County
curl "https://api.open-meteo.com/v1/forecast?latitude=28.4439&longitude=115.7539&daily=weather_code,temperature_2m_max,temperature_2m_min,precipitation_sum&forecast_days=7&timezone=auto"
```

### Fallback: wttr.in

```bash
# One-line summary
curl "wttr.in/Nanchang?format=3"

# Detailed current conditions
curl "wttr.in/Nanchang?0"

# 3-day forecast
curl "wttr.in/Nanchang"

# Week forecast
curl "wttr.in/Nanchang?format=v2"
```

### Format Options (wttr.in)

```bash
# One-liner with custom format
curl "wttr.in/London?format=%l:+%c+%t+%w"

# JSON output
curl "wttr.in/London?format=j1"

# PNG image
curl "wttr.in/London.png"
```

### Format Codes (wttr.in)

- `%c` — Weather condition emoji
- `%t` — Temperature
- `%f` — "Feels like"
- `%w` — Wind
- `%h` — Humidity
- `%p` — Precipitation
- `%l` — Location

## Weather Codes (WMO)

| Code | Description |
|------|-------------|
| 0 | Clear sky |
| 1, 2, 3 | Mainly clear, partly cloudy, overcast |
| 45, 48 | Fog, depositing rime fog |
| 51, 53, 55 | Drizzle: Light, moderate, dense |
| 61, 63, 65 | Rain: Slight, moderate, heavy |
| 71, 73, 75 | Snow fall: Slight, moderate, heavy |
| 80, 81, 82 | Rain showers: Slight, moderate, violent |
| 95, 96, 99 | Thunderstorm: Slight, moderate, heavy |

## Quick Responses

**"What's the weather?" (Default Location)**

```bash
# Open-Meteo
curl -s "https://api.open-meteo.com/v1/forecast?latitude=28.4439&longitude=115.7539&current=temperature_2m,weather_code,relative_humidity_2m&timezone=auto"

# wttr.in fallback
curl -s "wttr.in/Nanchang?format=%l:+%c+%t+(feels+like+%f),+%w+wind,+%h+humidity"
```

**"Will it rain?"**

```bash
# Open-Meteo with precipitation
curl -s "https://api.open-meteo.com/v1/forecast?latitude=28.4439&longitude=115.7539&current=precipitation,weather_code&timezone=auto"
```

**"Weekend forecast"**

```bash
# Open-Meteo 7-day forecast
curl "https://api.open-meteo.com/v1/forecast?latitude=28.4439&longitude=115.7539&daily=weather_code,temperature_2m_max,temperature_2m_min,precipitation_sum&forecast_days=7&timezone=auto"
```

## Response Format (Open-Meteo)

### Current Weather

```json
{
  "latitude": 28.4439,
  "longitude": 115.7539,
  "current": {
    "temperature_2m": 15,
    "relative_humidity_2m": 68,
    "weather_code": 2,
    "wind_speed_10m": 12,
    "precipitation": 0
  }
}
```

### Daily Forecast

```json
{
  "daily": {
    "time": ["2026-02-27", "2026-02-28"],
    "weather_code": [2, 61],
    "temperature_2m_max": [18, 16],
    "temperature_2m_min": [10, 12],
    "precipitation_sum": [0, 5.2]
  }
}
```

## Notes

- **Primary API**: Open-Meteo (free, no API key, global coverage)
- **Fallback**: wttr.in (for quick text responses)
- **Default Location**: 江西省南昌市南昌县 (28.4439, 115.7539)
- Rate limited; don't spam requests
- Works for most global cities
- Supports airport codes and coordinates

## Related Links

- **Open-Meteo Docs**: https://open-meteo.com/en/docs
- **Weather Codes**: https://open-meteo.com/en/docs#weather-codes
- **Geocoding API**: https://open-meteo.com/en/docs/geocoding-api
- **wttr.in Help**: https://wttr.in/:help
