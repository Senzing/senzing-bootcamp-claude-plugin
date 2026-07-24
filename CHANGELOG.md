# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
[markdownlint](https://dlaa.me/markdownlint/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.4.0] - 2026-07-24

### Added in 0.4.0

- Initial release of the `senzing-bootcamp` Claude Code plugin,
  distributed through the repository's plugin marketplace
- Guided bootcamp skills, from entity resolution concepts through graduation:
  - `bootcamp-onboarding` — onboarding flow, ground rules, module completion, feedback
  - `bootcamp-preparation` — environment readiness checks
  - `module-00-entity-resolution-concepts` — entity resolution primer *(optional)*
  - `module-01-business-problem` — discover and document the business problem
  - `module-02-sdk-setup` — SDK installation and configuration
  - `module-03-system-verification` — system verification
  - `module-03b-truthset-visualization` — interactive Truth Set web app *(optional)*
  - `module-04-data-collection` — identify and collect data sources
  - `module-05-data-quality-mapping` — data quality assessment, mapping, and test load
  - `module-06-data-processing` — build loading, load sources, and validate
  - `module-07-query-visualize-discover` — query, visualize, and discover
  - `graduation` — recap PDF and `production/` starter project
- Slash commands: `/start-bootcamp`, `/graduate`, and `/bootcamp-feedback`
- Hooks for `SessionStart`, `UserPromptSubmit`, `PreToolUse`, `Stop`,
  `PreCompact`, and `SessionEnd` to gate writes, capture feedback,
  and preserve bootcamp progress across compaction and sessions
- [Senzing MCP server](https://mcp.senzing.com/mcp) configuration for
  SDK code generation, Senzing fact lookup, and working examples
- Model selection guidance and an example bootcamp recap in `docs/`
- README with installation instructions for the Claude app and troubleshooting
