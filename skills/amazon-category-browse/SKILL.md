---
name: amazon-category-browse
description: Browse Amazon's category tree and the products inside a category with the Canopy MCP server. Use when the user wants to explore a department without a search term, asks what subcategories a category has, wants the products in a category sorted by price, rating, or newest, or needs a category id for another tool.
---

# Amazon category browse

Use the Canopy MCP server bundled with this plugin (server name `canopy`, endpoint `https://mcp.canopyapi.co/mcp`). Every tool is read-only. Do not scrape Amazon HTML, and do not recite Amazon's category tree from memory; ids differ by marketplace and change.

## Before calling a tool

1. Choose the marketplace. `domain` defaults to `US`. Valid values are `US`, `UK`, `CA`, `DE`, `FR`, `IT`, `ES`, `AU`, `IN`, `MX`, `BR`, `JP`, `PL`, `AE`, `SG`, `NL`, and `SE`. A category id belongs to one marketplace.
2. If a call fails because the client is not authenticated, stop and ask the user to sign in with Canopy or to set a Canopy API key in the client. Do not invent a key, and do not put a key in plugin files.

## Walking the tree

1. Call `get_amazon_categories` for the root departments. Each has `id`, `name`, `url`, and `breadcrumbPath`.
2. Call `get_amazon_category` with a `categoryId`. The response has the category's `name`, `breadcrumbPath`, `subcategories`, and `productResults`.
3. Repeat step 2 with a subcategory `id` to go deeper. Stop when `subcategories` is empty or when you reach the level the user asked about.

Go down one branch at a time, following the user's interest. Do not crawl the whole tree.

## Products in a category

`get_amazon_category` returns the category's products. Each has `title`, `asin`, `url`, `price`, `rating`, `ratingsTotal`, `isPrime`, and `sponsored`.

- `sort` is `FEATURED` (default), `MOST_RECENT`, `PRICE_ASCENDING`, `PRICE_DESCENDING`, or `AVERAGE_CUSTOMER_REVIEW`.
- Page with `page` while `productResults.pageInfo.hasNextPage` is true.

To filter a category by keyword, price band, or condition, call `search_amazon_products` with that `categoryId` and the filters. `get_amazon_category` has no such filters.

## Using category ids elsewhere

Ids from `get_amazon_categories` and `get_amazon_category` work as `categoryId` on `search_amazon_products`, `get_amazon_category`, and `get_amazon_deals`.

They do not work on `get_amazon_bestsellers`. Best-seller lists use their own ids, from `get_amazon_bestseller_categories`. For those, use the `amazon-best-sellers` skill.

## Reporting

Name the marketplace and give the category's `breadcrumbPath` so the user can see where it sits. List subcategories by name, with ids when the user will need them. For products, give title, ASIN, price, rating, and number of ratings, and link each `url`. Name the sort you used. A missing price or rating means the field was unavailable; do not treat it as zero.

## Gotchas

- `FEATURED` order is Amazon's merchandising order, not sales rank. Do not call the first products in a category its best sellers.
- A category id is usually a number but can be a string. Pass it exactly as returned.
- A subcategory entry can have a null `id`. It cannot be opened with `get_amazon_category`; say so and offer the entry's `url`.
