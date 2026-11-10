---
name: amazon-product-comparison
description: Compare two or more Amazon products side by side with the Canopy MCP server, on price, rating, number of ratings, specifications, availability, and estimated sales. Use when the user asks which of several products is better, asks for the difference between products, or wants a comparison table or a shortlist for a purchase.
---

# Amazon product comparison

Use the Canopy MCP server bundled with this plugin (server name `canopy`, endpoint `https://mcp.canopyapi.co/mcp`). Compare only on data that server returns. Do not scrape Amazon HTML, and do not fill a gap in one product's data from memory.

## Before calling a tool

1. Choose one marketplace for the whole comparison. `domain` defaults to `US`. Valid values are `US`, `UK`, `CA`, `DE`, `FR`, `IT`, `ES`, `AU`, `IN`, `MX`, `BR`, `JP`, `PL`, `AE`, `SG`, `NL`, and `SE`. Prices from different marketplaces are in different currencies and are not comparable.
2. For a product-scoped tool, pass exactly one of `asin`, `url`, or `gtin`.
3. If a call fails because the client is not authenticated, stop and ask the user to sign in with Canopy or to set a Canopy API key in the client. Do not invent a key, and do not put a key in plugin files.

## Workflow

1. Settle the list of products. If the user named products instead of giving ASINs or URLs, find each with `search_amazon_products` and confirm the match by title and brand. If the user gave a need instead of products ("a quiet blender under $100"), search for it and take up to five results where `sponsored` is not true.
2. Call `get_amazon_product` for every product, in parallel when the client allows it. This returns `title`, `brand`, `price`, `rating`, `ratingsTotal`, `isPrime`, `isInStock`, `featureBullets`, `technicalSpecifications`, and `coupon`.
3. Add other tools only for what the user asked about:
   - Other sizes, colors, or configurations: `get_amazon_product_variants`.
   - Cheaper offers from other sellers: `get_amazon_product_offers`.
   - Which sells more: `get_amazon_product_sales`.
   - Stock: `get_amazon_product_stock`.

Keep a comparison to five products or fewer unless the user asks for more.

## Building the table

Put one product per column and one attribute per row. Start with price, rating, number of ratings, Prime, and in stock. Then add the specifications that bear on the user's decision.

`technicalSpecifications` is a list of `name` and `value` pairs written by each seller, so names differ between products. Match rows by meaning ("Item Weight" and "Weight"), and convert units to one system before comparing. Leave a cell as "not listed" when a product does not give that specification. Do not infer it from the title or from a similar model.

## Recommending

If the user asked which to buy, give a recommendation and the reason, tied to what they said they care about. Name what the other products do better. If the data does not separate the products, say that.

Weigh `rating` against `ratingsTotal`: 4.8 from 40 ratings is weaker evidence than 4.5 from 12,000. Do not rank on star rating alone.

## Reporting

Name the marketplace. Link each product `url` and give its ASIN. Quote prices with currency. Mention a `coupon` when one is returned, without working out a final price unless the label states the amount. Say that sales and stock figures are Canopy estimates. A missing price or rating means the field was unavailable; do not treat it as zero.

## Gotchas

- Check that the products are the same kind of thing. A two-pack and a single unit, or different sizes of the same model, need a per-unit comparison.
- The price on `get_amazon_product` is the featured offer for that ASIN. A different variant has a different price.
- Feature bullets are seller marketing copy. Report them as the listing's claims.
