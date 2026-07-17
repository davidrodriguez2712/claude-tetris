---
name: weather
description: >
  Check current temperature and weather conditions for Ottawa, Canada using web
  search — no API key needed. Always targets Ottawa, Canada regardless of any
  city mentioned. Use when the user says "/weather", "clima", "weather", or
  asks for current conditions.
---

Report current weather for **Ottawa, Canada**, briefly. Always Ottawa — ignore any other city named in the invocation.

## Steps

1. City is fixed: **Ottawa, Canada**.
2. Call `WebSearch` with query: `Ottawa Canada current weather temperature now`.
3. Extract from results: temperature (°C, and °F if source gives it), conditions (clear/cloudy/rain/etc.), humidity, wind. Prefer the most recent/specific observation (e.g. named station, exact time) over vaguer secondary sources.
4. Reply in **2-3 lines max**: temperature, conditions, humidity/wind if available, one source link (markdown).

## Language

Reply in the same language the user used to invoke this skill (Spanish in, Spanish out — never translate the answer into a third language). Do not let the English tool-search results pull the reply into English or any other language — translate the extracted facts, don't echo source wording.

## Example

Input: `/weather` (no city, called from a Spanish-language session)
Output:
```
Ottawa: 24.9°C, parcialmente nublado. Humedad 57%, viento N 18 km/h.
Fuente: [The Weather Network](https://www.theweathernetwork.com/en/city/ca/ontario/ottawa/current)
```

## Boundaries

Single lookup, no forecasts/hourly breakdowns unless asked. Not a scheduler — for recurring checks use `/loop` or `/schedule` separately and pass this skill's instructions as the prompt.
