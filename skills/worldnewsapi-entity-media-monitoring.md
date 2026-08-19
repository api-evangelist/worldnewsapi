---
name: World News API — monitor an organization or person across the news
description: Track coverage of a company, person, or topic over a time window with sentiment filtering, then pull full article records for the hits worth reading. Use for brand monitoring, competitive tracking, and event watch.
api: openapi/_original/worldnewsapi-openapi-original.json
base_url: https://api.worldnewsapi.com
operations:
- searchNews
- retrieveNewsArticlesByIds
- searchNewsSources
mcp_tools:
- search_news
- retrieve_news_articles
- search_news_sources
generated: '2026-08-13'
method: generated
source: openapi/_original/worldnewsapi-openapi-original.json + https://worldnewsapi.com/docs/semantic-types/
---

# Entity and media monitoring

## Authenticate

`x-api-key` header (preferred) or `api-key` query parameter.

## Step 1 — decide keyword vs entity

`searchNews` supports two different matching modes and they are not interchangeable.

- `text` — literal keyword match on the article. Minimum 3 characters, maximum 100.
  Use `text-match-indexes` to restrict matching to the title rather than the whole body
  when you want headline-level relevance only.
- `entities` — semantic match on tagged entities, format `TYPE:Value`, comma separated,
  e.g. `ORG:Tesla,PER:Elon Musk`. This matches regardless of spelling or synonym, so
  `LOC:USA` catches "US", "United States" and "United States of America". Types are listed
  at https://worldnewsapi.com/docs/semantic-types/.

For monitoring a named organization or person, prefer `entities`. Fall back to `text` for
product names, phrases, and anything not in the entity vocabulary.

Note: `text-match-indexes` exists in the REST contract but is **not** exposed by the MCP
`search_news` tool, so an agent working over MCP cannot scope a text match to the title.

## Step 2 — bound the window and the tone

```
GET /search-news?entities=ORG:Tesla&language=en&earliest-publish-date=2026-08-01 00:00:00&latest-publish-date=2026-08-13 23:59:59&number=50&sort=publish-time&sort-direction=DESC
```

Add `min-sentiment` / `max-sentiment` (range [-1,1]) to isolate negative or positive
coverage — `max-sentiment=-0.2` is a reasonable first cut for reputational risk.

Other filters: `source-country`, `categories` (politics, sports, business, technology,
entertainment, health, science, lifestyle, travel, culture, education, environment, other),
`authors`, `news-sources` (comma-separated source URLs).

Always set `earliest-publish-date`. Without it the search runs against the full history —
back to 2022-01-01 on paid plans, 1 month on Free.

## Step 3 — confirm a source is monitored

If a stakeholder asks "are we seeing everything from *Publication X*?", call
`searchNewsSources` (`GET /search-news-sources?name=herald`) with a partial name. It answers
whether that publication is in the monitored set. It is the only honest way to answer a
coverage question — absence from search results is not proof of absence of coverage.

## Step 4 — pull full records

Search results already carry `title`, `summary`, `text`, `url`, `authors`, `publish_date`,
`sentiment`, `category`, `language` and `source_country`. When you only need full text for a
subset, collect the `id` values and batch them:

```
GET /retrieve-news?ids=206030983,206030984,206030985
```

`ids` is a comma-separated list. Batching is materially cheaper than one call per article —
each request costs a base point of its own.

## Cost discipline

Cost is 1 point per request plus 0.01 points per returned result. A 100-result search costs
2 points; ten 10-result searches cost 10. Prefer fewer, wider searches with tight filters
over many narrow ones, and read `X-API-Quota-Left` before each new batch.

## Known modeling limit

An article has no source id. `NewsArticle` links to a publication only through the `url`
host, so do not claim to have grouped coverage "by outlet" unless you grouped on the host
yourself or filtered with `news-sources` up front.

## Errors

401 bad key · 402 daily points exhausted, stop until midnight UTC · 403 feature not on this
plan · 404 unknown id · 429 rate or concurrency exceeded, back off exponentially, no
`Retry-After`. No error carries a response body.
