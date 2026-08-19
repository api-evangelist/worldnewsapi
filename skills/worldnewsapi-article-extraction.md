---
name: World News API — extract articles and links from any news website
description: Turn an arbitrary news URL into a structured article with text, images, videos and authors, or crawl a news homepage for its current article links. Use when the user supplies a URL rather than a search query.
api: openapi/_original/worldnewsapi-openapi-original.json
base_url: https://api.worldnewsapi.com
operations:
- extractNewsLinks
- extractNews
- newsWebsiteToRSSFeed
mcp_tools:
- extract_news_links
- extract_news
generated: '2026-08-13'
method: generated
source: openapi/_original/worldnewsapi-openapi-original.json + https://worldnewsapi.com/docs/extract-news/
---

# Article and link extraction

These operations work on **any** news URL, whether or not the publication is in the World
News API index. That makes them the right tool when the user hands you a link, and the
wrong tool when the user asks a question — for questions, search the index instead
(`searchNews`), which is cheaper and returns semantic metadata.

## Authenticate

`x-api-key` header (preferred) or `api-key` query parameter.

## Extract one article

`extractNews` (`GET /extract-news`):

```
GET /extract-news?url=https://www.bbc.com/news/world-us-canada-59340789&analyze=true
```

- `url` is **required**.
- `analyze=true` adds entity extraction and sentiment. It costs more points. Leave it off
  when you only need the text.

The response carries `title`, `text`, `url`, `publish_date`, `language`, `authors[]`,
`images[]` (each with `url`, `title`, `width`, `height`) and `videos[]` (each with `url`,
`title`, `summary`, `thumbnail`, `duration`).

**Use the plural collections.** The scalar `author`, `image` and `video` fields are still
present in the contract but were announced as deprecated on 2024-06-07 with removal
scheduled for 2024-12-31. Reading them is a liability; they may disappear without another
notice.

An extracted article has **no `id`**. Extraction is stateless and the result is not written
into the index, so you cannot retrieve it again with `retrieveNewsArticlesByIds`. If you
need the article later, keep the payload.

## Discover links on a news homepage

`extractNewsLinks` (`GET /extract-news-links`):

```
GET /extract-news-links?url=https://www.theguardian.com&analyze=false
```

Returns `news_links`, an array of URLs. The specification declares it as an untyped array,
so treat the items as plain strings and validate before use.

The normal pattern is: `extractNewsLinks` on the section front, filter the links you care
about, then `extractNews` on each — but note that each extraction is its own billable
request. Filter hard before you fan out.

## Turn a website into a feed

`newsWebsiteToRSSFeed` (`GET /feed.rss?url=...`) produces an RSS 2.0 document for a news
site. It returns **XML, not JSON**, and it is the one operation with no MCP tool — an agent
must call the REST endpoint directly and parse XML.

## Check the index first

Before extracting at volume, call `searchNewsSources` (`GET /search-news-sources?name=...`)
to see whether the publication is already monitored. If it is, searching the index is
cheaper and gives you sentiment, categories and entity tags that extraction only provides
with `analyze=true`.

## Cost and errors

1 point per request plus 0.01 per returned result, with `analyze=true` costing more.
Extraction of many URLs is the most expensive thing you can do with this API — budget it
against `X-API-Quota-Left` before starting a loop.

401 bad key · 402 daily allowance exhausted, stop until midnight UTC · 403 feature not on
this plan · 404 the URL could not be fetched or parsed · 406 unacceptable representation ·
429 rate or concurrency exceeded, back off exponentially with no `Retry-After`. No error
response carries a body.
