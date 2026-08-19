---
name: World News API — local news search around a place
description: Find news published or mentioning a specific place by resolving the place name to coordinates, then searching within a radius. Use when a user asks for news "near", "around", or "in" a city, neighborhood, or landmark.
api: openapi/_original/worldnewsapi-openapi-original.json
base_url: https://api.worldnewsapi.com
operations:
- getGeoCoordinates
- searchNews
mcp_tools:
- get_geo_coordinates
- search_news
generated: '2026-08-13'
method: generated
source: openapi/_original/worldnewsapi-openapi-original.json + https://worldnewsapi.com/docs/search-news/
---

# Local news search

World News API has no "city" parameter. Local search is always two calls: resolve the
place, then filter by a radius around the resulting point.

## Authenticate

Send the API key as the `x-api-key` request header. The `api-key` query parameter also
works, but the provider recommends the header in production so the key stays out of logs
and browser history. There is no OAuth and no token refresh.

## Step 1 — resolve the place

Call `getGeoCoordinates` (`GET /geo-coordinates`) with `location` set to the address or
place name exactly as the user said it.

```
GET /geo-coordinates?location=Kreuzberg, Berlin
```

The response is `{latitude, longitude, city}`. If the returned `city` is not the place the
user meant, ask before continuing — do not guess a second spelling and burn another point.

Cache this result. Coordinates for a place do not change, and every call costs points.

## Step 2 — search within a radius

Call `searchNews` (`GET /search-news`) with `location-filter` built from step 1 as
`latitude,longitude,radius_km`. The radius must be between 1 and 100 km.

```
GET /search-news?location-filter=52.4996,13.4184,10&language=en&number=20&sort=publish-time&sort-direction=DESC
```

Useful narrowing parameters, all optional: `text` (at least 3 characters), `language`
(ISO 639-1), `source-country` (ISO 3166 alpha-2), `categories`, `earliest-publish-date` and
`latest-publish-date` (`YYYY-MM-DD HH:MM:SS`), `min-sentiment` / `max-sentiment` in [-1,1].

`location-filter` matches news that *mentions* a location as well as news published there,
so a wide radius over a major city returns national coverage. Start at 10–25 km and widen
only if the result count is too low.

## Step 3 — page if needed

The response envelope is `{offset, number, available, news}`. `available` is the total
match count. Page with `offset` (0–100000) and `number` (1–100, default 10). Ask for the
largest page you will actually use: cost is 1 point per request **plus 0.01 points per
result returned**, so requesting 100 and using 5 wastes budget.

## Budget and failure handling

Read `X-API-Quota-Left` on every successful response and stop before it reaches zero.

- **402** — the daily point allowance is exhausted. Stop. On the Free plan nothing will
  succeed until midnight UTC. Do not retry in a loop.
- **429** — the per-second or concurrent-request ceiling was exceeded. Back off
  exponentially. No `Retry-After` header is sent.
- **401** — the key is missing or wrong. Stop and report; retrying will not help.
- **403** — the plan does not include the feature.

Error responses carry **no body** — the status code is the entire error contract.

## Via MCP

The same flow through the first-party stdio MCP server (`npm install -g
world-news-api-mcp`) is `get_geo_coordinates` then `search_news` with the identical
hyphenated parameter names. There is no hosted remote endpoint; a human must install the
server first.
