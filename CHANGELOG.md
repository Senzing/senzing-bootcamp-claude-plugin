# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
[markdownlint](https://dlaa.me/markdownlint/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.4.1] - 2026-07-27

### Added in 0.4.1

- `generate_discoveries_pdf.py` — renders a Module 7 discoveries document to a
  branded PDF, laying Markdown tables out as a grid
- `normalize_docs_markdown.py` — normalizes generated Markdown under `docs/`
  before it is rendered
- Expanded model and reasoning-effort guidance in
  `docs/model-selection.md`

### Changed in 0.4.1

- Re-rate the recommended model and reasoning effort for every module, and
  pause only when the recommendation differs from what the bootcamper chose
- Honor every saved setup preference, not just model guidance
- Render recap and discoveries tables as a grid, and keep a ragged row from
  desynchronizing the columns
- Serve the standalone snapshot's Records action from the embedded graph nodes
  instead of a second, duplicate copy of every entity's records
- Build the Records table's columns from the fields the endpoint returned, so no
  always-empty Name/Address/Phone columns read as missing data

### Fixed in 0.4.1

- Keep every captured tab in the recap, in the order the app presents them, and
  stop the recap silently downgrading its renderer when a title is clipped
- Correct MCP calls in Module 5 that could never have succeeded, and gate the
  license request
- Stop the data-quality analyzer treating a supported record shape as a
  structural failure, and flag partial `response_schemas` rows
- Fix the Records action in a standalone snapshot, which reported "No records
  returned for this entity" for every entity while the live server showed them
- Fix a crash in the visualization app's "Show all merged entities" list, which
  aborted the list at the first entity carrying a match key
- Refuse a non-local `--url` in `capture_screenshots.py` before the tab
  pre-flight reads the page, rather than after it had already been fetched
- Classify exactly 500 records as the `demo` tier in Module 6, matching
  `sdk_guide`'s own single-threaded cutover, and pass `language` to the
  `sdk_guide` call in step 4
- Use US English spelling throughout the plugin content and scripts, and extend
  the cspell dictionary to cover the remaining technical terms

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
