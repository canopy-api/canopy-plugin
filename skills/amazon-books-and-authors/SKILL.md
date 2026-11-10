---
name: amazon-books-and-authors
description: Look up books and authors on Amazon with the Canopy MCP server, including ISBN lookup, an author's biography and catalogue, and a book's formats and editions. Use when the user gives an ISBN, asks about a book or an author, wants the books by an author, or asks which formats a title is sold in.
---

# Amazon books and authors

Use the Canopy MCP server bundled with this plugin (server name `canopy`, endpoint `https://mcp.canopyapi.co/mcp`). Every tool is read-only. Do not scrape Amazon HTML, and do not fill in a title, author, or edition the tools left out.

## Before calling a tool

1. Choose the marketplace. `domain` defaults to `US`. Valid values are `US`, `UK`, `CA`, `DE`, `FR`, `IT`, `ES`, `AU`, `IN`, `MX`, `BR`, `JP`, `PL`, `AE`, `SG`, `NL`, and `SE`. A book's ASIN, price, and availability differ by marketplace.
2. If a call fails because the client is not authenticated, stop and ask the user to sign in with Canopy or to set a Canopy API key in the client. Do not invent a key, and do not put a key in plugin files.

## Finding a book

- By ISBN: call `get_amazon_product` with the ISBN as `gtin`. Strip hyphens and spaces first. To get only the ASIN, call `get_amazon_asin_from_gtin`.
- By title or author name: call `search_amazon_products` with the title, or the title and author, as `searchTerm`. Confirm the match by title before using its ASIN.
- ISBN for a known book: call `get_amazon_gtin_from_asin`. Kindle editions usually have no ISBN, so a null result is normal for them.

## Formats and editions

Call `get_amazon_product_variants` with the book's ASIN. Each variant has its own `asin`, a `text` label such as Kindle, Hardcover, Paperback, or Audiobook, and a `price`. Call `get_amazon_product` on a variant's ASIN for that format's details.

## Author pages

`get_amazon_author` returns the author's `name`, `biography`, `imageUrl`, and `bookResults`. Each book result has `title`, `asin`, `url`, `price`, `rating`, `ratingsTotal`, and `authors`. Page with `page` while `bookResults.pageInfo.hasNextPage` is true.

The tool takes the author's ASIN, not a book's ASIN and not a name. Get the author ASIN from one of these:

- An Amazon author page URL the user supplied. The ASIN is the path segment after `/author/` or `/e/`, for example `B017M7UJX6`.
- An `authors[].url` in another author's `bookResults`, for a co-author.
- An author link in any other tool response, when one is present.

If none of these is available, ask the user for the author's Amazon page. Do not guess an author ASIN, and do not pass a book's ASIN in its place. Without an author ASIN you can still list an author's books with `search_amazon_products`; say that this is a search and may miss titles or include other authors.

## Reporting

Name the marketplace. For each book give the title, the format, the ASIN, and the ISBN when you have it, and link the `url`. Quote prices per format. Quote `rating` with `ratingsTotal` as the number of ratings. A missing price or rating means the field was unavailable; do not treat it as zero.

## Gotchas

- An ISBN maps to one format. The hardcover and paperback of the same title have different ISBNs and different ASINs.
- `get_amazon_asin_from_gtin` returns a null ASIN when Canopy has no match on that marketplace. Try the marketplace where the edition was published before saying the book is not on Amazon.
- For book best-seller lists, use the `amazon-best-sellers` skill.
