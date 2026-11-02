# Tool reference

Nine tools. All return JSON text. Most successful responses carry a `note` field. Inputs are validated against the ranges below.

## Plans in the output

| Field | Free account | Pro |
|---|---|---|
| `revenueRange` | A band such as `$10K–$25K` or `$1M+` (null below $10K) | not returned |
| `downloadsRange` | A band such as `5K–10K` or `1M+` (null below 1K) | not returned |
| `latestRevenueUsd` | not returned | Monthly revenue as one number, or null if the app shows none |
| `latestDownloads` | not returned | Monthly downloads as one number, or null |
| `price`, `rating`, `ratingCount` | not returned | returned by `find_top_earning_apps`, `search_apps`, `find_chart_movers` and `get_app` |

Tools marked **Pro** return `{"error": "subscription_required", "upgradeUrl": "..."}` for a free key. The call still counts against the 20 monthly calls.

## Shared parameter: `category`

A category name (`"Health & Fitness"`, `"Finance"`, `"Food & Drink"`), a short name (`"fitness"`, `"puzzle games"`) or a numeric genre id. If the name is unknown or ambiguous the tool returns `{"error": "category_not_found", "categories": [...]}` with names to retry.

Valid names: Business, Weather, Utilities, Travel, Sports, Social Networking, Reference, Productivity, Photo & Video, News, Navigation, Music, Lifestyle, Health & Fitness, Games, Finance, Entertainment, Education, Books, Medical, Magazines & Newspapers, Food & Drink, Shopping, Developer Tools, Graphics & Design, and the game genres Action, Adventure, Casual, Board, Card, Casino, Family, Puzzle, Racing, Role Playing, Simulation, Strategy, Trivia, Word.

## Shared parameter: `app`

An App Store URL (`https://apps.apple.com/.../id123456789`), a numeric App Store id, or an app name (1 to 200 characters). A name resolves to the best search match. Unknown apps return `{"error": "app_not_found"}`.

## `find_new_winning_apps`

Apps released in the last 6 months that already earn $10K or more a month, highest first. Without a category, games are left out; pass `Games` to include them.

| Parameter | Type | Notes |
|---|---|---|
| `category` | string, optional | See above |
| `limit` | integer 1–50, default 20 | |

Returns `category`, `apps[]` with `id`, `name`, `publisher`, `category`, `releasedAt`, `profileUrl` and the revenue and download fields for your plan.

## `find_top_earning_apps`

Highest-earning apps by monthly revenue, in one category or across all.

| Parameter | Type | Notes |
|---|---|---|
| `category` | string, optional | |
| `limit` | integer 1–50, default 20 | |

Same shape as above. Pro also gets `price`, `rating`, `ratingCount`.

## `find_chart_movers`

Daily App Store chart movement: apps that climbed the most places and apps that entered the list since an earlier day.

| Parameter | Type | Notes |
|---|---|---|
| `country` | enum, default `us` | Two-letter code. 36 countries: us, gb, de, fr, ca, au, jp, kr, br, tr, es, it, mx, tw, nl, se, ch, sg, hk, ae, sa, no, dk, pl, ru, in, id, th, nz, ie, at, be, za, il, cl, co |
| `chart` | `grossing` \| `free` \| `paid`, default `grossing` | |
| `category` | string, optional | Leave out for the overall list |
| `date` | `YYYY-MM-DD`, optional | UTC. Default: latest day |
| `days` | integer 1–28, default 1 | Compare with the newest chart at least this many days earlier: 1 is day over day, 7 is week over week |
| `limit` | integer 1–50, default 20 | Applies to risers and new entries separately |

Returns `chartDate`, `comparedWith`, `listSize`, `risers[]` (`rank`, `previousRank`, `change`, app fields) and `newEntries[]` (`rank`, app fields). A `warning` appears when there is nothing to compare with, or when a new entry may only be new to AppHitlist's catalog.

Coverage: overall lists exist for every chart kind in every country. Category lists: us, gb and de have grossing, free and paid; fr, ca, au, jp, kr, br, tr, es, it, mx, tw, nl, se, ch and sg have grossing and free; the other countries have grossing only. Raw daily lists are kept for 35 to 60 days. An unavailable chart returns `{"error": "chart_not_available", ..., "latestChartDate", "hint"}`.

## `search_apps`

Search by app or developer name.

| Parameter | Type | Notes |
|---|---|---|
| `query` | string 2–80 | Required |
| `limit` | integer 1–10, default 5 | |

Returns `apps[]` with the same fields as above (Pro also gets `price`, `rating`, `ratingCount`).

## `get_app`

One app. Free: base fields and revenue and download ranges. Pro adds `price`, `currency`, `rating`, `ratingCount`, `hasInAppPurchases`, `storeUrl`, the first 500 characters of the description and `rankSummary` (best and latest chart rank per chart over the last 30 days).

| Parameter | Type | Notes |
|---|---|---|
| `app` | string | Required |

## `find_longest_running_ads` (Pro)

Active Meta (Facebook and Instagram) ads that have been running the longest. Long-running ads usually mean the ad pays for itself.

| Parameter | Type | Notes |
|---|---|---|
| `category` | string, optional | |
| `limit` | integer 1–20, default 10 | |

Returns `ads[]` with `app` (`id`, `name`, `category`, `profileUrl`), `title`, `body`, `format`, `cta`, `linkUrl`, `platforms`, `startedAt`, `daysRunning`, `copies`, longest running first.

## `get_app_ads` (Pro)

Active Meta ads for one app, longest running first.

| Parameter | Type | Notes |
|---|---|---|
| `app` | string | Required |
| `limit` | integer 1–20, default 10 | |

Returns `totalAds` and `ads[]` (`title`, `body`, `format`, `cta`, `linkUrl`, `platforms`, `startedAt`, `endedAt`, `daysRunning`, `copies`).

## `get_category_complaints` (Pro)

The most common complaint topics from 1 and 2 star reviews across all apps in one category. Each topic shows how many reviews and apps mention it, with example apps.

| Parameter | Type | Notes |
|---|---|---|
| `category` | string | Required |
| `kind` | `product` \| `billing` \| `price`, optional | Only complaints of that kind |
| `limit` | integer 1–30, default 15 | |

Returns `appsAnalyzed` and `themes[]`.

## `get_app_complaints` (Pro)

Top complaint themes from one app's 1 and 2 star reviews.

| Parameter | Type | Notes |
|---|---|---|
| `app` | string | Required |

Returns up to 10 `themes[]` with `theme`, `summary`, `kind` and `reviewCount`.

## Errors and limits

| Result | Meaning |
|---|---|
| HTTP 401 | Missing, invalid or revoked key |
| Tool result with `isError` and a "used all 20 free calls" message | Monthly free limit reached; resets at the start of the next UTC month |
| Tool result with `isError` and "Rate limit reached" | More than 60 calls in a minute on Pro; retry after the time given |
| `subscription_required`, `app_not_found`, `category_not_found`, `chart_not_available` | See above |
