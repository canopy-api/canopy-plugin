---
name: amazon-best-sellers
description: Pull Amazon best-seller lists and rank positions with the Canopy MCP server. Use when the user asks for the top sellers in a category, what is ranking in a department, a product's best-seller rank, or how a best-seller list compares across marketplaces.
---

# Amazon best sellers

Use the Canopy MCP server bundled with this plugin (server name `canopy`, endpoint `https://mcp.canopyapi.co/mcp`). Report only the ranks that server returns. Do not scrape Amazon HTML, and do not estimate a rank the tools left out.

## Before calling a tool

1. Choose the marketplace. `domain` defaults to `US`. Valid values are `US`, `UK`, `CA`, `DE`, `FR`, `IT`, `ES`, `AU`, `IN`, `MX`, `BR`, `JP`, `PL`, `AE`, `SG`, `NL`, and `SE`.
2. If a call fails because the client is not authenticated, stop and ask the user to sign in with Canopy or to set a Canopy API key in the client. Do not invent a key, and do not put a key in plugin files.

## Workflow

1. Find the category. Call `get_amazon_bestseller_categories` and match the user's department to a returned `name`. Use that entry's `id`.
2. Fetch the list. Call `get_amazon_bestsellers` with `categoryId`. If the user gave an Amazon best-sellers page URL, pass it as `url` and skip step 1.
3. Narrow if needed. The response's `categoryInfo` lists `childCategories`, each with an `id`. Call `get_amazon_bestsellers` again with a child `id` to reach a subcategory such as "Coffee Machines" under "Kitchen".
4. Page with `page` while `pageInfo.hasNextPage` is true. A page typically holds 20–50 products. Use `limit` when the user only wants the top few.

Each result carries `bestSellersRank`, `title`, `asin`, `url`, `price`, `rating`, and `ratingsTotal`.

## Finding one product's rank

`get_amazon_product` does not return a best-seller rank. To answer "where does this product rank":

1. Call `get_amazon_product` and read its `categories`. Each has a `name` and a `breadcrumbPath`; the longest path is the most specific category.
2. Find the matching best-seller category by name, starting from `get_amazon_bestseller_categories` and walking `childCategories`.
3. Page through `get_amazon_bestsellers` for that category and look for the ASIN.

If the ASIN is not in the list, say it does not appear in the best sellers Canopy returned for that category. Do not report that as a rank.

## Reporting

Name the marketplace and the category, including the parent category when there is one. List products in rank order with rank, title, ASIN, price, rating, and number of ratings, and link each product `url`. A rank is a position within one category on one marketplace at the time of the call; say so when comparing lists. A missing price or rating means the field was unavailable; do not treat it as zero.

## Gotchas

- Best-seller category ids are not the ids from `get_amazon_categories`. Passing a browse category id to `get_amazon_bestsellers` returns the wrong list or nothing.
- `get_amazon_bestsellers` needs `categoryId` or `url`. It has no keyword search.
- For unit sales behind a rank, use the `amazon-sales-and-demand` skill.
