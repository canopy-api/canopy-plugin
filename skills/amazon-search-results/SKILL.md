---
name: amazon-search-results
description: Analyze Amazon search results for a keyword with the Canopy MCP server, including result order, sponsored placements, filters, and related queries. Use when the user asks what shows up for a search term, where a product or brand appears for a keyword, which results are sponsored, or wants keyword suggestions.
---

# Amazon search results

Use the Canopy MCP server bundled with this plugin (server name `canopy`, endpoint `https://mcp.canopyapi.co/mcp`). Report only the results that server returns. Do not scrape Amazon HTML, and do not reconstruct a results page from memory.

## Before calling a tool

1. Choose the marketplace. `domain` defaults to `US`. Valid values are `US`, `UK`, `CA`, `DE`, `FR`, `IT`, `ES`, `AU`, `IN`, `MX`, `BR`, `JP`, `PL`, `AE`, `SG`, `NL`, and `SE`.
2. Use the user's search term as written. Do not correct, translate, or expand it unless they ask, because a changed term returns a different page.
3. If a call fails because the client is not authenticated, stop and ask the user to sign in with Canopy or to set a Canopy API key in the client. Do not invent a key, and do not put a key in plugin files.

## Running a search

Call `search_amazon_products` with `searchTerm`. Optional parameters:

- `sort`: `FEATURED` (default), `MOST_RECENT`, `PRICE_ASCENDING`, `PRICE_DESCENDING`, or `AVERAGE_CUSTOMER_REVIEW`. Leave it at `FEATURED` for any question about where something appears, since that is the order shoppers see.
- `minPrice` and `maxPrice`: a price band.
- `conditions`: a comma-separated list of `NEW`, `USED`, and `RENEWED`.
- `categoryId`: restrict to a department. Ids come from `get_amazon_categories`.
- `page` and `limit`: a page typically holds 20–40 products.

Each result has `title`, `asin`, `url`, `price`, `rating`, `ratingsTotal`, `isPrime`, `sponsored`, and `coupon`. The response also carries `availableRefinements`, the filters Amazon offers for that query, and `pageInfo`.

## Finding where a product appears

1. Search with `sort` left at `FEATURED` and no price or condition filter.
2. Number the results in the order returned, starting at 1 on each page.
3. Look for the ASIN. If it is not on page 1, page while `pageInfo.hasNextPage` is true. Stop after three pages unless the user asks for more.
4. Report the page and the position on that page. Give the position among all results and, separately, among results where `sponsored` is not true.

If the ASIN was not found, say how many pages you checked. Do not say the product does not rank.

## Related queries

Call `get_amazon_autocomplete` with a `searchTerm` to get the suggestions Amazon shows as a shopper types. Use short prefixes to widen the set. Suggestions show what shoppers type; they carry no search volume, so do not present them as volume data.

## Reporting

Name the marketplace, the exact search term, the sort, and any filters. Show results as a numbered list in returned order with title, ASIN, price, rating, and number of ratings, and mark sponsored results. Link each product `url`. Say that results reflect one request at one time and that Amazon varies results by shopper and location. A missing price or rating means the field was unavailable; do not treat it as zero.

## Gotchas

- Sponsored results are ads. Count them separately from organic positions.
- `pageInfo.totalResults` counts the whole query, not the results on the page you fetched.
- `AVERAGE_CUSTOMER_REVIEW` sorts by star rating, which favors products with few ratings. Read `ratingsTotal` next to `rating`.
- For the price of one known product, use the `amazon-price-and-deals` skill.
