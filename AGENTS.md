# AGENTS.md

Guidance for coding agents (Claude Code, Codex, Cursor, and others) working in this repository.

## What this repo is

The Canopy agent plugin: marketplace manifests, an MCP server declaration, and skills. It contains no code. Every client is pointed at the hosted server `https://mcp.canopyapi.co/mcp`, whose source is [canopy-api/canopy-api-mcp](https://github.com/canopy-api/canopy-api-mcp). Tool changes belong in that repo; this one changes when a manifest, a skill, or a listing changes.

## Allowed files (hard rule)

Anthropic's directory refuses any version of this plugin that contains code or installs dependencies, and the plugin folder is the repository root. So every file committed here must be one of:

- Markdown, plain text, JSON, or YAML, saved as UTF-8 with no NUL bytes
- a PNG, JPEG, GIF, WebP, or ICO image
- `LICENSE`, `README.md`, `.gitignore`

Never add `package.json`, a lockfile, a package manager config (such as `pnpm-workspace.yaml`), a script or program in any language, or any file that starts with `#!`. JSON files must be strict JSON with no comments. This is why validation runs as inline steps in `.github/workflows/validate.yml` and not from a script file.

## Layout

- `.claude-plugin/plugin.json` — Claude Code manifest. `.claude-plugin/marketplace.json` — the marketplace catalog (`canopy-api`), with the plugin at `./`.
- `.cursor-plugin/plugin.json` — Cursor Marketplace manifest. Points `mcpServers` at `.mcp.json`.
- `.grok-plugin/plugin.json` — Grok Build manifest, read by the xAI catalog indexer.
- `.mcp.json` — MCP server for Claude Code, Cursor, and Grok (`type: http`).
- `plugin.json` and `mcp.json` — Agent Plugins 1.0.0 manifest and MCP declaration (`type: streamable-http`).
- `skills/<name>/SKILL.md` — one directory per skill. The frontmatter `name` must match the directory.
- `assets/canopy-icon.png` — listing icon referenced by the manifests.
- `submissions/` — publishing steps and the xAI catalog entry.

Server declarations never carry credentials: no `headers`, no API keys. Auth is OAuth discovered from the server.

## Versioning and changelog

Every PR updates `CHANGELOG.md` ([Keep a Changelog](https://keepachangelog.com/en/1.1.0/) format). Merging to `main` is a release: the marketplaces read `main` directly, and `claude plugin update` only picks up a release when the version in `.claude-plugin/plugin.json` changes.

The version lives in four files and all four must match: `plugin.json`, `.claude-plugin/plugin.json`, `.cursor-plugin/plugin.json`, and `.grok-plugin/plugin.json`. CI fails if they disagree. The plugin version is independent of the server version.

- **Major**: removing or renaming a skill, or changing the plugin or marketplace name.
- **Minor**: a new skill or a new marketplace listing.
- **Patch**: wording changes to skills, descriptions, and metadata.

After merge, tag the merge commit `vX.Y.Z` and push the tag.

## Validation

```bash
claude plugin validate . --strict
grok plugin validate .
```
