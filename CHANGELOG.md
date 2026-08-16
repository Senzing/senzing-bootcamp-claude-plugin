# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
[markdownlint](https://dlaa.me/markdownlint/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.5.1] - 2026-08-16

### Added in 0.5.1

- A `UserPromptSubmit` hook, `checkpoint-tick.py`, creates
  `docs/progress/recap_checkpoint.md` within a turn of the bootcamp starting and
  reminds the guide once to keep it current — a bootcamp becomes active *after*
  `SessionStart` has already run, so nothing earlier can guarantee the file
  exists, and a full ten-module bootcamp ran without one
- `capture_screenshots.py --single` captures a page that has no tabs as one
  whole-document image; a single-page deliverable used to request six tabs that
  do not exist and write nothing
- Capture writes a `<name>-tabs.json` manifest beside the PNGs, and the recap's
  `--check` reads it for an **external** denominator — `embedded N of M images`
  takes both numbers from the recap being measured, so it cannot see a tab that
  was captured and then never referenced. With no manifest the check reports
  itself skipped rather than passed
- Capture skips a tab the app suppresses because its data does not exist, rather
  than writing a near-empty pane under a confident slug and counting it as
  covered
- `generate_document_pdf.py` renders any bootcamp Markdown in the house style,
  and `generate_discoveries_pdf.py` gains `--subtitle`, `--require-sections` and
  `--no-section-check` to support it
- Graduation renders the business-problem and discoveries documents as styled
  PDF keepsakes, verifying each rather than trusting the `PDF generated:` line
- Module 2 offers an SDK update when a newer release exists, with the mechanism
  each platform family actually uses — apt, yum/dnf, Homebrew cask, Scoop, and
  the Docker image tag — and states that Senzing documents no 4.x → 4.y update
  procedure rather than implying one is unnecessary
- The container lifecycle hooks handle podman and Apple's `container` CLI
  alongside Docker, dispatching on the runtime recorded with each container;
  Docker Desktop cannot be installed non-interactively, so a macOS Apple Silicon
  bootcamp may legitimately run under a different runtime
- Feedback is triaged as `plugin`, `mcp-server`, `both`, `host` or `unclear`,
  and a `host` finding is recorded locally but never forwarded
- A silent in-run feedback append for a documented behavior the engine's own
  output contradicts, filed when it happens rather than recalled at graduation
- The source-color encoding widens past the first palette cycle along stroke
  color, stroke width, then fill lightness, and warns instead of colliding
  silently beyond its stated capacity
- `senzing_viz_server.py` validates the engine settings it resolved and names
  the missing `PIPELINE` keys, instead of proceeding into an engine abort
- Module 5 documents `embedded_master` — a second entity hiding in a column —
  how to declare it, and going `back` as the sanctioned fix when it was missed
- Module 1 opens with a data-privacy reminder, and builds its use-case gallery
  from sector vocabulary rather than the category label, which returns
  confidently wrong content for two homonym categories

### Changed in 0.5.1

- Recap bullet lists are spaced by default, and where the gap falls is decided
  **structurally** by indentation rather than by a list of subsection names: a
  response stays with its question, one Q/R pair is separated from the next, and
  "Files produced" — the recap's own index, 5-12 glossed paths — is no longer
  the one list rendered as an undifferentiated block
- The write gate blocks any resolved target outside the project. System temp and
  Downloads no longer decide *whether* to block, only which message is shown
- A bootcamp is "active" only when the progress file records a `current_module`.
  The preface writes that file empty, so its mere existence had `SessionStart`
  announcing a bootcamp in progress on a project with no module to resume
- `/start-bootcamp` decides resume-versus-start from what the progress file
  contains, and treats a file recording no module as a normal fresh start —
  silently, not as a corruption to report
- The model/effort nudge names only the dial that differs, in the answer hint as
  well as the question; reads the dial before instructing a value the bootcamper
  has already set; and treats an effort above every row in the table (`xhigh`,
  `max`) as satisfied, since the step-down clause would otherwise fire at every
  remaining module and answering it could not make it stop
- "Graduation" is named **Bootcamp graduation** throughout, and
  `bootcamp-preparation` gains its own model-selection row so every stage the
  bootcamp can run has exactly one
- Module 0's quiz is offered as a knowledge check: the benefit, never the
  assessment
- Example query chips are verified concurrently rather than one at a time, so up
  to ten live engine round-trips no longer sit in front of the app's first paint
- The Truth Set server is stopped by the process id captured at launch, never by
  matching its command line
- The feedback trigger recognizes "I have some feedback about module 5" and
  fault language that names the bootcamp, plugin, module or tutorial — and
  deliberately still ignores bare "this is broken", which in Modules 5-7 almost
  always means the bootcamper's own code
- README states the plan the bootcamp needs (Claude Max 5x, or several Pro
  windows) and the data that suits it
- Module 3 separates the seven installation checks from results validation, and
  results validation gains an `expectation_mismatch` outcome — the engine
  working and the prediction being wrong is not a failed install
- Modules 6 and 7 reconcile every per-source record count against that source's
  own input before presenting it, and write the discrepancy instead of the count
  when the two disagree
- A `brand_tokens` import failure says which failure occurred — absent, or
  present but unusable — rather than dropping to the fallback palette in silence

### Fixed in 0.5.1

- Recognize a recap image written as a list item (`- ![alt](path)`), which
  `module-completion.md` asks for: a recap of 8 captured screenshots embedded 0
  at exit 0, with `--check` reporting "captured 8, referenced 0" — reading as
  though the guide had forgotten to embed them
- Stop the stdlib PDF writers substituting `?` for 24 of the 33 characters the
  sanitizer maps (`≥ ≤ ≈ ≠ € ™ ∞ ← ↔ ⇒ ↑ ↓ ✅ ✓ ⚠`), silently and at exit 0 with
  a green retention figure. Sanitization moved to the token boundary, the second
  substitution table is gone, and a character that still cannot be encoded is
  dropped **and reported**
- Stop `--single` cropping a tall page: Chrome's `--window-size` and Selenium's
  `set_window_size` set the outer window, not the viewport, so a full-height
  request lost the footer while still reporting a full-page capture. The offset
  is measured and added back, and a capture that fell short says so in its label
- Block a write outside the project that named neither temp nor Downloads,
  including one reached by a `..` escape, which the gate had been allowing
- Prefer a complete `SENZING_ENGINE_CONFIGURATION_JSON` over an incomplete
  `config/engine_config.json`: a `{"PIPELINE": {}}` stub is valid JSON, so it
  won on existence and the run then failed with `SENZ7426`, an error whose
  documented meaning sends the reader to check a `SUPPORTPATH` that is correct
- Try `NAME_ORG` after a failing `NAME_FULL` search instead of returning at the
  first error, and report a failure only when every candidate is exhausted
- Key the graph's stroke on its width rather than its palette cycle, so a node
  and its legend swatch cannot disagree, and the 25th source is not drawn
  identically to the 7th
- Resolve the plugin version inside the feedback hook rather than handing the
  guide `${CLAUDE_PLUGIN_ROOT}`, which is not substituted in injected text and,
  on a machine carrying two plugin roots, resolves to the wrong checkout
- Sanitize certificate and stdlib PDF text before measuring and wrapping it —
  a transliteration changes the length, which mis-centered lines and overran
  wraps
- Emit the recap's inter-item gap after a list item's *last* source line, so an
  item whose Markdown wraps across two source lines no longer loses its gap
- Report a fold that did nothing, and say which nothing: a checkpoint that was
  never created and one the guide never wrote to are different failures, and
  neither used to be distinguishable from a successful fold
- Take the discoveries renderer's cover title and subtitle from the document, so
  another document no longer ships with "What Senzing found in your data" on its
  stakeholder-facing cover

## [0.5.0] - 2026-07-30

### Added in 0.5.0

- Windows is supported end to end: PowerShell install instructions for the
  Claude Code CLI, ground rules covering the PowerShell 5.1 encoding and syntax
  traps, and Chrome/Edge discovery in their Windows install locations and the
  registry's `App Paths` so screenshot capture works where neither browser is on
  `PATH`
- A vocabulary rule for naming the Claude interface — **Claude Desktop**,
  **Claude Code CLI**, **the Claude web app**, **a Claude IDE extension** — so a
  model or effort nudge names the controls the bootcamper actually has; "the
  Claude app" is retired
- `normalize_docs_markdown.py` detects and reports Windows-1252 mojibake, a
  corruption that is valid UTF-8 and that no other check flags
- Both PDF generators report every character the built-in fonts had to drop,
  naming each one and the passage it came from
- `generate_recap_pdf.py --preferences` reads the certificate name the
  bootcamper was asked for, which outranks the recap header
- `--check` validates the three labeled blocks inside each End-of-Module Summary
  rather than only the heading, and the renderer prints an absent block as
  "(not recorded)" instead of dropping it
- The recap reports `embedded N of M images` and names every image it could not
  embed
- `senzing_viz_server.py --dataset` states what the loaded data is, so a saved
  snapshot no longer calls the bootcamper's own data "this Truth Set"
- A canonical bash-and-zsh path-resolution pattern for the environment script,
  with fail-loudly guards, in Module 2
- Module 2 records the chosen `database_type` in preferences — the key Modules 4
  and 6 read for their SQLite warnings
- Module 5 offers a `getRecordPreview` readiness check, including the
  undocumented data-source registration it requires
- Module 0 quiz guidance: numbered multiple-choice items, wrong answers named as
  wrong and re-taught, and a pinned follow-up wording for further questions
- Captured example visualizations alongside the example recap

### Changed in 0.5.0

- Search tries `NAME_FULL` then `NAME_ORG`, and an empty result names the
  attributes searched — an organization name matches nothing under `NAME_FULL`
  and returns no error, so half a mixed dataset was silently unsearchable
- Example query chips are verified against the live engine before being offered,
  and any that return nothing are dropped rather than shipped as dead controls
- Graph node labels are disambiguated when truncation collides, with the full
  name on hover; the distinctness rule now binds every truncated label, not just
  match keys
- Entity Graph captures get a longer settle budget, and re-activating the
  already-active tab no longer restarts its force simulation
- `--tabs` and `--help` name only the six tabs the app actually serves
- An export may or may not carry `RELATED_ENTITIES` depending on its flag set —
  Modules 4, 6, and 7 now say to dump a row and route on what is there, instead
  of stating flatly that exports never carry it
- `reporting_guide` calls name their topic (`evaluation` for statistics,
  `export` for extraction); `topic='reports'` targets a data mart the bootcamp
  never builds
- A method's own default-flags composite is not `SZ_ENTITY_DEFAULT_FLAGS`:
  Module 7 records which sub-flags search and network defaults omit, and adds
  "correct field name, wrong flags" as a distinct cause of a blank value
- Module 5 scores completeness per record against the features that apply to
  that record's `RECORD_TYPE`, rather than averaging every feature across a
  mixed person/organization source
- Module 5 source-qualifies the mapping-phase Markdown it relocates, so a second
  source cannot overwrite — or append to — the first source's durable record
- Module 5's quality and mapping pages are held to the same brand, offline,
  escaping, and verify-the-render rules as the visualization app
- Module 2 cites both `generate_scaffold` workflows the verification script
  needs, and how to fetch snippet source from `raw_url`
- Preference answers are held and written once per step instead of once per
  answer
- The Truth Set snapshot is titled after its own module, not System Verification
- Recap image paths are written relative to the recap — `visualizations/…`,
  never `docs/visualizations/…`
- `_esc_html` escapes quotes, so it is safe in an attribute value and not only
  in a text node

### Fixed in 0.5.0

- Fix relative recap image paths resolving from the working directory instead of
  the recap's own directory, which dropped every screenshot while the success
  line still reported ~99% content retained
- Distinguish "no browser found" from "a browser was found and every capture
  failed", and name the locations searched — a Windows machine carrying both
  Chrome and Edge was told no capability was available
- Stop `--check` accepting an End-of-Module Summary written as prose, which
  reached the keepsake with all three labeled blocks absent
- Split the `in progress` marker off a folded module title, which rode into a
  width measurement and silently degraded the whole recap to the fallback
  renderer, and made `--check` report one section as both found and missing
- Print a non-Latin-1 certificate name by folding it, or warn and ask, rather
  than printing `??` on a bootcamper's certificate
- Stop the standalone snapshot pointing at a hardcoded port 8080, and stop it
  offering example searches that returned no match as pre-verified examples
- Break long-form labeled callouts onto their own line in the discoveries PDF
  instead of hanging them under wherever the label ended
- Correct Module 3b's `get_sample_data` calls, which omitted the required
  `dataset` parameter and so reported nothing about availability

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
