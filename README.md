# Canopy Plugin — Amazon Product Data for AI Agents

The Canopy plugin for Claude Code, Claude.ai, Cursor, Grok Build, and other [Agent Plugins](https://agent-plugins.org/specification) clients. It connects your agent to live Amazon data from the [Canopy API](https://canopyapi.co/): search, prices, Buy Box offers, ratings, deals, best sellers, sales estimates, and ASIN lookup.

The plugin holds no code. It points each client at the hosted Canopy MCP server, `https://mcp.canopyapi.co/mcp`, and ships ten skills that teach the agent how to use it. The server itself lives in [canopy-api/canopy-api-mcp](https://github.com/canopy-api/canopy-api-mcp): go there for the tool list, authentication details, self-hosting, and server issues.

## What you get

- **The Canopy MCP server**, with 16 read-only Amazon data tools.
- **Ten skills** that teach the agent which tools to call and how to report the results:

| Skill | Use it for |
| --- | --- |
| `amazon-product-research` | Product details, variants, stock, ratings, and ASIN/GTIN conversion. The general skill and tool index. |
| `amazon-price-and-deals` | Prices, Buy Box offers, coupons, and current deals. |
| `amazon-best-sellers` | Best-seller lists by category and a product's rank in them. |
| `amazon-books-and-authors` | ISBN lookup, a book's formats and editions, and an author's catalogue. |
| `amazon-search-results` | What appears for a search term, in what order, which results are sponsored, and related queries. |
| `amazon-sales-and-demand` | Unit sales estimates for a product and the size of a niche. |
| `amazon-ratings` | A product's star rating and number of ratings, and the top-rated products for a search or category. |
| `amazon-seller-research` | A seller's profile, rating, and catalogue, and who holds the Buy Box. |
| `amazon-product-comparison` | Side-by-side comparison of products on price, ratings, and specifications. |
| `amazon-category-browse` | The category tree and the products inside a category. |

The plugin does not embed an API key. OAuth-capable clients discover sign-in from the server and open Canopy's consent page on first use. If a client only supports a static header, set `CANOPY-API-KEY` in that client. Get a key at [canopyapi.co](https://canopyapi.co/).

## Install

### Claude Code

This repository is also a Claude Code marketplace:

```bash
claude plugin marketplace add canopy-api/canopy-plugin
claude plugin install canopy@canopy-api
```

Or, inside a session: `/plugin marketplace add canopy-api/canopy-plugin`, then `/plugin install canopy@canopy-api`. For one session without installing, run `claude --plugin-dir /path/to/canopy-plugin`. On first use, approve the Canopy OAuth prompt.

If you added the marketplace from the old location (`canopy-api/canopy-api-mcp`), remove it first with `claude plugin marketplace remove canopy-api`.

### Claude.ai and Cowork

After the plugin is listed in Anthropic's directory, add **Canopy** from the directory and sign in on the plugin's Connectors tab. The plugin uses the same URL as the existing [Canopy connector](https://claude.ai/directory/canopy).

### Cursor

Install **Canopy** from Customize or the [Cursor Marketplace](https://cursor.com/marketplace) after it is listed. To try it before then, copy this repository to `~/.cursor/plugins/local/canopy` (a symlink that points outside that folder is ignored), reload the window, and confirm the Canopy server and all ten skills in Customize.

### Grok Build

After the plugin is in the [xAI plugin marketplace](https://github.com/xai-org/plugin-marketplace), install it from `/marketplace` or:

```bash
grok plugin install canopy --trust
```

### Other Agent Plugins clients

This repository is an Agent Plugin (specification 1.0.0). Those clients read root `plugin.json` and `mcp.json`. There is no archive and no registry protocol in the specification: the package is this directory.

### No plugin support?

Add the MCP server directly. See the [server README](https://github.com/canopy-api/canopy-api-mcp#quick-start) or the [Amazon MCP server guide](https://canopyapi.co/amazon-mcp-server) for per-client setup.

## Repository layout

```
canopy-plugin/
├── .claude-plugin/
│   ├── plugin.json             # Claude Code plugin manifest
│   └── marketplace.json        # Claude Code marketplace catalog
├── .cursor-plugin/plugin.json  # Cursor Marketplace manifest
├── .grok-plugin/plugin.json    # Grok Build plugin manifest
├── .mcp.json                   # Claude Code, Cursor, and Grok MCP server (type http)
├── plugin.json                 # Agent Plugins 1.0.0 manifest
├── mcp.json                    # Agent Plugins MCP declaration (type streamable-http)
├── skills/
│   ├── amazon-product-research/
│   │   ├── SKILL.md
│   │   └── references/tools.md # tool parameters
│   └── <nine more skills>/     # one SKILL.md each; see "What you get"
├── assets/canopy-icon.png      # listing icon
└── submissions/                # how to publish to each marketplace
```

## Contributing

Validate before you push:

```bash
claude plugin validate . --strict
grok plugin validate .
```

CI also checks that every manifest carries the same `version`. Publishing steps for each marketplace are in [`submissions/README.md`](submissions/README.md), and the rules for what may be committed here are in [`AGENTS.md`](AGENTS.md).

## License

Released under the [MIT License](LICENSE). Copyright (c) 2026 Canopy API.

## Related

- [Canopy MCP server source](https://github.com/canopy-api/canopy-api-mcp)
- [Amazon MCP Server — Canopy](https://canopyapi.co/amazon-mcp-server)
- [Canopy API docs](https://docs.canopyapi.co)
