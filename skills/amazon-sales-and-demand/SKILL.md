---
name: amazon-sales-and-demand
description: Estimate how well an Amazon product or niche sells with the Canopy MCP server, using unit sales estimates, stock estimates, best-seller position, and ratings. Use when the user asks how many units a product sells, how much revenue it makes, how big a niche or market is, or whether there is demand for a product idea.
---

# Amazon sales and demand

Use the Canopy MCP server bundled with this plugin (server name `canopy`, endpoint `https://mcp.canopyapi.co/mcp`). Sales and stock figures from this server are Canopy estimates, not Amazon-reported numbers. Say so every time you quote one. Do not scrape Amazon HTML, and do not produce a sales figure the tools did not return.

## Before calling a tool

1. Choose the marketplace. `domain` defaults to `US`. Valid values are `US`, `UK`, `CA`, `DE`, `FR`, `IT`, `ES`, `AU`, `IN`, `MX`, `BR`, `JP`, `PL`, `AE`, `SG`, `NL`, and `SE`. Sales are per marketplace.
2. For a product-scoped tool, pass exactly one of `asin`, `url`, or `gtin`.
3. If a call fails because the client is not authenticated, stop and ask the user to sign in with Canopy or to set a Canopy API key in the client. Do not invent a key, and do not put a key in plugin files.

## One product

1. `get_amazon_product_sales` returns `salesEstimate` with `weeklyUnitSales`, `monthlyUnitSales`, and `annualUnitSales`.
2. `get_amazon_product` returns the current `price`, `rating`, `ratingsTotal`, and `categories`.
3. `get_amazon_product_stock` returns `stockEstimate` with `inStock`, `stockLevel`, and `availabilityMessage`. Call it when the user asks about inventory or sell-through.

Revenue is not returned by any tool. If the user asks for it, multiply the unit estimate by the current price, show the two inputs, and label the result a rough estimate. It ignores price changes, promotions, and other sellers' offers at other prices.

A product with variants has one sales estimate per ASIN. Use `get_amazon_product_variants` to list the child ASINs, then fetch sales for the ones that matter. Tell the user which ASINs the figure covers.

## A niche

1. Pick the products that define the niche. For a keyword, call `search_amazon_products` and use the first page of results where `sponsored` is not true. For a category, use `get_amazon_bestsellers` as described in the `amazon-best-sellers` skill.
2. Fetch `get_amazon_product_sales` for each. Keep it to the top 10 unless the user asks for more, and make the calls in parallel when the client allows it.
3. Report the per-product estimates in a table, then the total and the share held by the top three.

State the sample: which products, how they were chosen, and on what marketplace. A total over ten products is the size of those ten products, not of the market.

## Reading the numbers

- Many ratings with low current sales points to an older product past its peak. High sales with few ratings points to a recent launch.
- One product holding most of the sample's sales means the niche is concentrated, which matters more to a new entrant than the total.
- A low `stockLevel` against weekly sales suggests a stock-out soon; it does not show demand by itself.

Present these as readings of the estimates, not as facts about the product.

## Reporting

Name the marketplace and each ASIN, and link the product `url`. Give units with their period: per week, per month, or per year. Say that sales and stock figures are Canopy estimates. A missing estimate means Canopy has no figure for that product; do not treat it as zero and do not substitute a guess.

## Gotchas

- Estimates are for a point in time. Seasonal products look very different across the year, so do not multiply a weekly figure into an annual one; use `annualUnitSales`.
- The tools return no sales history. Do not describe a trend from a single call.
- `stockLevel` is tied to one offer, identified by `offerId`, not to every seller on the listing.
