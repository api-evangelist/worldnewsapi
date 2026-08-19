---
name: World News API — build a daily country news briefing
description: Assemble what mattered in a country on a given day from clustered top headlines plus the newspaper front pages that ran that morning. Use for daily briefings, situation reports, and country monitoring.
api: openapi/_original/worldnewsapi-openapi-original.json
base_url: https://api.worldnewsapi.com
operations:
- topNews
- retrieveNewspaperFrontPage
- retrieveNewsArticlesByIds
mcp_tools:
- get_top_news
- retrieve_newspaper_front_page
- retrieve_news_articles
generated: '2026-08-13'
method: generated
source: openapi/_original/worldnewsapi-openapi-original.json + https://worldnewsapi.com/docs/top-news/
---

# Daily country briefing

## Authenticate

`x-api-key` header (preferred) or `api-key` query parameter.

## Step 1 — clustered top news

`topNews` (`GET /top-news`) returns the day's stories for one country in one language,
already clustered so that multiple outlets covering the same event arrive as one group.

```
GET /top-news?source-country=us&language=en&date=2026-08-12
```

`source-country` (ISO 3166 alpha-2) and `language` (ISO 639-1) are **required**. `date`
(`YYYY-MM-DD`) is optional and defaults to the current day. Set `headlines-only=true` when
you only need id, title and url — it materially shrinks the response and the per-result
point cost.

The response is `{top_news: [{news: [...]}], language, country}`. Each element of `top_news`
is one cluster and the first article in a cluster is the representative story; the rest are
other outlets' coverage of the same event. Report cluster size — it is the best available
proxy for how much a story dominated the day.

Clusters carry no id or title of their own, so a cluster cannot be tracked across days.
Do not tell a user you are "following a story"; you are re-clustering each day.

## Step 2 — front pages

`retrieveNewspaperFrontPage` (`GET /retrieve-front-page`) returns scanned front pages for
6,000+ publications in 125+ countries.

```
GET /retrieve-front-page?source-country=us&source-name=new-york-post&date=2026-08-12
```

All three parameters are optional but supply `source-country` at minimum. The earliest
available date is **2024-07-09** — asking for anything earlier returns 404, not an empty
result. Front pages are **not on the Free plan**; a Free key gets 403 here.

The response is `{front_page: {name, date, country, image, language}}` — `image` is a URL to
the scan, so a briefing can embed it directly.

## Step 3 — depth on the stories that matter

Take the `id` values from the clusters worth expanding and pull them in one batch:

```
GET /retrieve-news?ids=206030983,206030984
```

## Assembling the briefing

1. Lead with the largest cluster.
2. Give each story one line from `summary` and attribute to the `url` host.
3. Report the range of `sentiment` across a cluster rather than a single article's score —
   one outlet's tone is not the story's tone.
4. Attach the front-page image as the visual anchor.
5. State the date and the country explicitly; `topNews` without `date` silently means today
   in UTC terms, which is not the reader's today.

## Cost and errors

Base cost is 1 point per request plus 0.01 per result — a full country briefing with front
pages is typically 3–5 points. Watch `X-API-Quota-Left`.

401 bad key · 402 daily allowance exhausted, stop until midnight UTC · 403 front pages not
included in this plan · 404 no front page for that publication or date · 429 back off
exponentially, no `Retry-After`. Errors carry no response body.
