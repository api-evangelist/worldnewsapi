# World News API (worldnewsapi)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

World News API is a real-time and historical news data API covering thousands of sources across 210+ countries and 86+ languages. It provides full-text and semantic news search, geo-targeted **local news search** (a radius filter around a latitude/longitude point), article content and link extraction from arbitrary URLs, country-level top news clustering, newspaper front-page images, and news-source discovery.

Local news is a first-class use case: resolve a place name to coordinates with the **Geo Coordinates** endpoint, then pass those coordinates to **Search News** via the `location-filter` parameter (`latitude,longitude,radius-in-km`, radius 1-100 km) to find news published or mentioned near that place.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/worldnewsapi/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/worldnewsapi/refs/heads/main/apis.yml)

## Access Model (Honest Summary)

- **Public, commercial REST API.** All endpoints are HTTP `GET` requests to `https://api.worldnewsapi.com`. No SDK is required, though official clients exist for 20+ languages ([ddsky/world-news-api-clients](https://github.com/ddsky/world-news-api-clients)) along with an MCP server.
- **API-key auth.** Sign up for a free account to get a key, passed either as the `api-key` query parameter or the `x-api-key` HTTP header.
- **Point-metered.** Every call consumes a number of points (credits) against a **daily** allowance. The **Free** plan gives 50 points/day and then blocks further calls until the next day; it is limited to 1 month of history and requires a backlink to worldnewsapi.com. Paid plans (Reporter $39, Journalist $379, Editor $1,779 per month) raise the daily allowance, bill per additional point, unlock unlimited history back to 2022, and remove the backlink requirement.
- **Rate limits scale with plan:** 1/2/10/20 requests per second and 1/5/10/20 concurrent requests across Free/Reporter/Journalist/Editor.
- **No WebSocket / no streaming.** The API is request/response REST only. "Real-time" means the corpus is continuously updated and queried on demand — there is no server-push (WebSocket or SSE) transport. See `review.yml`.
- Pricing and limits were captured from the public pricing page on 2026-07-11 and are **not** independently reconciled; verify current numbers at [worldnewsapi.com/pricing](https://worldnewsapi.com/pricing/).

## Tags

- News
- Local News
- News Search
- Media Monitoring
- Geo Search
- News Data
- Sentiment Analysis
- Content Extraction

## Timestamps

- **Created:** 2026-07-11
- **Modified:** 2026-07-11

## APIs

### World News Search News API

Search and filter news by keyword, semantic entities, category, language, source country, and publish-date range. Supports **local news search** via the `location-filter` parameter, which draws a 1-100 km radius around a latitude/longitude point (e.g. `51.509865,-0.118092,15` for 15 km around London). Returns title, text, summary, image, publish date, authors, language, source country, category, and sentiment, with `offset`/`number` pagination up to 100 results.

- **Human URL:** [https://worldnewsapi.com/docs/search-news/](https://worldnewsapi.com/docs/search-news/)
- **Base URL:** `https://api.worldnewsapi.com`

#### Tags

- News Search
- Local News
- Geo Search
- Semantic Search

#### Properties

- [Documentation](https://worldnewsapi.com/docs/search-news/)
- [API Reference](https://worldnewsapi.com/docs/search-news/)
- [OpenAPI](openapi/worldnewsapi-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/worldnewsapi.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/worldnewsapi.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### World News Geo Coordinates API

Resolve a free-form location name (e.g. `Tokyo, Japan`) to its latitude, longitude, and canonical city name. The returned coordinates feed the Search News `location-filter` parameter to power local and geo news search.

- **Human URL:** [https://worldnewsapi.com/docs/get-geo-coordinates/](https://worldnewsapi.com/docs/get-geo-coordinates/)
- **Base URL:** `https://api.worldnewsapi.com`

#### Tags

- Geo Search
- Local News
- Geocoding

#### Properties

- [Documentation](https://worldnewsapi.com/docs/get-geo-coordinates/)
- [OpenAPI](openapi/worldnewsapi-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/worldnewsapi.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/worldnewsapi.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### World News Top News API

Retrieve the top news for a country and language on a given day, clustered so stories covered by more outlets rank higher. Supports a `headlines-only` mode for lightweight requests.

- **Human URL:** [https://worldnewsapi.com/docs/top-news/](https://worldnewsapi.com/docs/top-news/)
- **Base URL:** `https://api.worldnewsapi.com`

#### Tags

- Top News
- Headlines
- Media Monitoring

#### Properties

- [Documentation](https://worldnewsapi.com/docs/top-news/)
- [OpenAPI](openapi/worldnewsapi-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/worldnewsapi.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/worldnewsapi.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### World News Extract News API

Extract a news article's title, text, images, publish date, and authors from any URL (`extract-news`), or extract the set of article links from a news website page (`extract-news-links`), both with optional named-entity analysis.

- **Human URL:** [https://worldnewsapi.com/docs/extract-news/](https://worldnewsapi.com/docs/extract-news/)
- **Base URL:** `https://api.worldnewsapi.com`

#### Tags

- Content Extraction
- Scraping
- Article Parsing

#### Properties

- [Documentation](https://worldnewsapi.com/docs/extract-news/)
- [Documentation](https://worldnewsapi.com/docs/extract-news-links/)
- [OpenAPI](openapi/worldnewsapi-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/worldnewsapi.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/worldnewsapi.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### World News Retrieve News API

Retrieve full information for one or more news articles by their ids — obtained from Search News or Top News — returning title, text, summary, URL, image, publish date, authors, language, category, and sentiment.

- **Human URL:** [https://worldnewsapi.com/docs/retrieve-news/](https://worldnewsapi.com/docs/retrieve-news/)
- **Base URL:** `https://api.worldnewsapi.com`

#### Tags

- Retrieve
- Articles
- News Data

#### Properties

- [Documentation](https://worldnewsapi.com/docs/retrieve-news/)
- [OpenAPI](openapi/worldnewsapi-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/worldnewsapi.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/worldnewsapi.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### World News Sources API

Find and inspect the news sources available in the World News API by name, for building source allow-lists and understanding coverage.

- **Human URL:** [https://worldnewsapi.com/docs/search-news-sources/](https://worldnewsapi.com/docs/search-news-sources/)
- **Base URL:** `https://api.worldnewsapi.com`

#### Tags

- News Sources
- Discovery
- Media Monitoring

#### Properties

- [Documentation](https://worldnewsapi.com/docs/search-news-sources/)
- [OpenAPI](openapi/worldnewsapi-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/worldnewsapi.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/worldnewsapi.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### World News Front Pages API

Retrieve newspaper front-page images by country, newspaper identifier, and date, for media monitoring and visual news archives.

- **Human URL:** [https://worldnewsapi.com/docs/newspaper-front-pages/](https://worldnewsapi.com/docs/newspaper-front-pages/)
- **Base URL:** `https://api.worldnewsapi.com`

#### Tags

- Front Pages
- Newspapers
- Images

#### Properties

- [Documentation](https://worldnewsapi.com/docs/newspaper-front-pages/)
- [OpenAPI](openapi/worldnewsapi-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/worldnewsapi.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/worldnewsapi.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [Authentication](authentication/worldnewsapi-authentication.yml)
- [Domain Security](security/worldnewsapi-domain-security.yml)
- [GitHub Organization](https://github.com/ddsky)
- [LinkedIn](https://www.linkedin.com/company/world-news-api)
- [Website](https://worldnewsapi.com)
- [Documentation](https://worldnewsapi.com/docs/)
- [Plans](plans/worldnewsapi-plans-pricing.yml)
- [Rate Limits](rate-limits/worldnewsapi-rate-limits.yml)
- [Fin Ops](finops/worldnewsapi-finops.yml)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
