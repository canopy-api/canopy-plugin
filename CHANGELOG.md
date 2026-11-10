# Changelog

All notable changes to the Canopy plugin are documented here. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and the project uses
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Versions up to 2.2.0 were released from [canopy-api/canopy-api-mcp](https://github.com/canopy-api/canopy-api-mcp/blob/main/CHANGELOG.md), where the plugin lived beside the server source.

## [Unreleased]

## [2.2.1] - 2026-11-09

### Changed

- The plugin moved to its own repository, `canopy-api/canopy-plugin`, which holds only manifests, skills, and the icon. Claude Code users add the marketplace with `claude plugin marketplace add canopy-api/canopy-plugin`.
- The Agent Plugins manifest (`plugin.json`) is named `canopy`, matching the other manifests. It was `canopy-api-mcp`.

[Unreleased]: https://github.com/canopy-api/canopy-plugin/compare/v2.2.1...HEAD
[2.2.1]: https://github.com/canopy-api/canopy-plugin/releases/tag/v2.2.1
