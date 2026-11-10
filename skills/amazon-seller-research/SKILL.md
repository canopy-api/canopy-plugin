---
name: amazon-seller-research
description: Research an Amazon third-party seller with the Canopy MCP server, including the seller's profile, rating, storefront catalogue, and Buy Box position on a listing. Use when the user asks who sells a product, wants to look into a seller or storefront, asks what a seller's catalogue contains, or asks who holds the Buy Box.
---

# Amazon seller research

Use the Canopy MCP server bundled with this plugin (server name `canopy`, endpoint `https://mcp.canopyapi.co/mcp`). Every tool is read-only. Do not scrape Amazon HTML, and do not fill in seller details the tools left out.

## Before calling a tool

1. Choose the marketplace. `domain` defaults to `US`. Valid values are `US`, `UK`, `CA`, `DE`, `FR`, `IT`, `ES`, `AU`, `IN`, `MX`, `BR`, `JP`, `PL`, `AE`, `SG`, `NL`, and `SE`. A seller id belongs to one marketplace.
2. If a call fails because the client is not authenticated, stop and ask the user to sign in with Canopy or to set a Canopy API key in the client. Do not invent a key, and do not put a key in plugin files.

## Getting a seller id

`get_amazon_seller` takes a `sellerId` such as `A34JY1ZNKUG942`. It does not take a seller name. Get the id from one of these:

- `get_amazon_product` on a product the seller lists: `seller.sellerId` is the featured seller.
- `get_amazon_product_offers` on that product: each offer has `seller.id` and `seller.name`.
- A storefront URL the user supplied: the id is the value of the `seller` or `me` query parameter.

If the user gave only a seller name, ask for a product that seller lists or for the storefront URL. Do not guess an id.

## Seller profile and catalogue

`get_amazon_seller` returns `name`, `rating`, `about`, `logoUrl`, `phone` when the seller lists one, and `productResults`. Each product has `title`, `asin`, `url`, `price`, `rating`, and `ratingsTotal`. Page with `page` while `productResults.pageInfo.hasNextPage` is true. `pageInfo.totalResults` gives the catalogue size when it is returned.

## Who sells a listing

Call `get_amazon_product_offers` with the product's `asin`, `url`, or `gtin`. Each offer has:

- `seller`: `name`, `id`, `rating`, `ratingsPercentagePositive`, and `ratingsTotal`.
- `price`, plus `delivery.price` for shipping.
- `conditionIsNew`, `isPrime`, and `delivery.fulfilledByAmazon`.
- `buyboxWinner`: true on the offer that holds the Buy Box.

Page with `page` when there are more offers.

## Buy Box across a seller's catalogue

To see how often a seller holds the Buy Box, take ASINs from the seller's `productResults`, call `get_amazon_product_offers` for each, and check whether the `buyboxWinner` offer's `seller.id` matches. Keep it to 10 ASINs unless the user asks for more, and report the count out of the number checked.

## Reporting

Name the marketplace, the seller name, and the seller id. Seller ratings are for the seller; product ratings are for the product. Label which is which. Give `ratingsPercentagePositive` with `ratingsTotal` next to it. Link product `url` values. A missing rating or phone number means the field was unavailable; do not treat it as zero or as a sign the seller is new.

## Gotchas

- An offer can come back with no `seller` or no `seller.id`, which is common for Amazon's own retail offer. There is no storefront to look up for it; report the offer and say the seller id was not returned.
- The Buy Box changes through the day. Report the holder as of the call.
- For the price comparison between offers, use the `amazon-price-and-deals` skill. For unit sales of a seller's products, use the `amazon-sales-and-demand` skill.
