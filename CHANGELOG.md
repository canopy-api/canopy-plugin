# Changelog

All notable changes to the Canopy plugin are documented here. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and the project uses
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Versions up to 2.2.0 were released from [canopy-api/canopy-api-mcp](https://github.com/canopy-api/canopy-api-mcp/blob/main/CHANGELOG.md), where the plugin lived beside the server source.

## [Unreleased]

## [2.3.0] - 2026-11-09

### Added

- Eight skills, each a workflow over tools the server already has:
  - `amazon-best-sellers`: best-seller lists by category and a product's rank in them.
  - `amazon-books-and-authors`: ISBN lookup, formats and editions, and author catalogues.
  - `amazon-search-results`: result order for a search term, sponsored placements, and related queries.
  - `amazon-sales-and-demand`: unit sales estimates for a product and the size of a niche.
  - `amazon-ratings`: a product's star rating and number of ratings, and top-rated products for a search or category.
  - `amazon-seller-research`: seller profiles, storefront catalogues, and the Buy Box holder.
  - `amazon-product-comparison`: side-by-side comparison of products.
  - `amazon-category-browse`: the category tree and the products inside a category.

### Changed

- `amazon-product-research` is now the general skill and tool index. Its description is narrower so the new skills are chosen for their topics, and it lists them.
- The README, the manifest descriptions and keywords, and the xAI catalog entry list ratings, not reviews, among the data the plugin provides.
- The README describes all ten skills.

### Removed

- `amazon-product-research` and its tools reference no longer mention `get_amazon_product_top_reviews`. The skills cover ratings, not review text.

## [2.2.1] - 2026-11-09

### Changed

- The plugin moved to its own repository, `canopy-api/canopy-plugin`, which holds only manifests, skills, and the icon. Claude Code users add the marketplace with `claude plugin marketplace add canopy-api/canopy-plugin`.
- The Agent Plugins manifest (`plugin.json`) is named `canopy`, matching the other manifests. It was `canopy-api-mcp`.

[Unreleased]: https://github.com/canopy-api/canopy-plugin/compare/v2.3.0...HEAD
[2.3.0]: https://github.com/canopy-api/canopy-plugin/compare/v2.2.1...v2.3.0
[2.2.1]: https://github.com/canopy-api/canopy-plugin/releases/tag/v2.2.1
