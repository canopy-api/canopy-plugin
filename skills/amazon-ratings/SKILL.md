---
name: amazon-ratings
description: Look up Amazon star ratings and rating counts with the Canopy MCP server, and find the top-rated products for a search term or category. Use when the user asks how a product is rated, how many ratings it has, which products are rated highest, or whether a rating can be trusted given its count.
---

# Amazon ratings

Use the Canopy MCP server bundled with this plugin (server name `canopy`, endpoint `https://mcp.canopyapi.co/mcp`). Report only the ratings that server returns. Do not scrape Amazon HTML, and do not quote a rating from memory; ratings change.

## Before calling a tool

1. Choose the marketplace. `domain` defaults to `US`. Valid values are `US`, `UK`, `CA`, `DE`, `FR`, `IT`, `ES`, `AU`, `IN`, `MX`, `BR`, `JP`, `PL`, `AE`, `SG`, `NL`, and `SE`. The same product can have a different rating and count on each marketplace.
2. For a product-scoped tool, pass exactly one of `asin`, `url`, or `gtin`.
3. If a call fails because the client is not authenticated, stop and ask the user to sign in with Canopy or to set a Canopy API key in the client. Do not invent a key, and do not put a key in plugin files.

## What the data is

Two fields describe a product's rating:

- `rating`: the average star rating, out of 5.
- `ratingsTotal`: the number of ratings behind that average.

They appear on `get_amazon_product`, and on every product in the results of `search_amazon_products`, `get_amazon_category`, `get_amazon_bestsellers`, `get_amazon_seller`, and `get_amazon_author`.

The tools return the average and the count. They do not return the split by star level, and this skill does not cover the text of customer reviews. If the user asks what customers say, or what share of ratings are one star, say that this data is not available here.

## One product

Call `get_amazon_product` and report `rating` and `ratingsTotal` together.

## Top-rated products

For a keyword, call `search_amazon_products` with `sort` set to `AVERAGE_CUSTOMER_REVIEW`. For a category, call `get_amazon_category` with the same `sort`. That value is the name of Amazon's sort order; it sorts by average star rating.

That sort puts a product with a 5.0 average from three ratings above one with 4.7 from 20,000. So do not report the first results as the top rated without checking counts:

1. Fetch the first page or two.
2. Drop results where `sponsored` is true.
3. Set a floor for `ratingsTotal` that fits the category, and tell the user what it is. A few hundred suits most consumer categories; use less for niche products.
4. Rank what remains by `rating`, breaking ties by `ratingsTotal`.

If the user wants popular and well rated, start from `FEATURED` search results or from `get_amazon_bestsellers` and filter by `rating` instead.

## Reading a rating

- A count in the thousands makes the average stable. A count under about 50 can move a lot with a few new ratings; say so.
- Variants of one listing usually share a rating, so the same figure on two sizes or colors is not independent evidence.
- A difference of 0.1 stars between two products with large counts is rarely meaningful.

Present these as readings of the numbers, not as facts about the product.

## Seller ratings

A seller's rating is a different thing from a product's. `get_amazon_seller` returns the seller's `rating`. Each offer from `get_amazon_product_offers` has `seller.rating`, `seller.ratingsPercentagePositive`, and `seller.ratingsTotal`. Label a seller rating as such, and use the `amazon-seller-research` skill for seller questions.

## Reporting

Name the marketplace and the ASIN, and link the product `url`. Always give the count next to the average, for example "4.6 out of 5 from 12,340 ratings". Say the figures are as of the call. A missing `rating` or `ratingsTotal` means the product has no ratings yet or the field was unavailable; do not treat it as zero stars.

## Gotchas

- `ratingsTotal` counts ratings, not written reviews. Do not call it a review count.
- Do not compare a rating from one marketplace against one from another without naming both.
- For a side-by-side of several products on more than ratings, use the `amazon-product-comparison` skill.
