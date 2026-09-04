# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
[markdownlint](https://dlaa.me/markdownlint/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.5.3] - 2026-09-04

### Added in 0.5.3

- **Bootcamp packaging.** A `/package-bootcamp` command and a `packaging.md`
  workflow in `bootcamp-onboarding` gather the bootcamp into **one zip under
  `backups/packages/`** that can be archived, moved to another machine, or handed
  to a colleague. Two profiles: `share` carries the results — recap PDF, keepsake
  documents, visualizations and `production/`, with no database, no source data
  and no credentials — and `transfer` adds the revisit bundle, config and
  mappings so the bootcamp can be resumed elsewhere. Available at any point, not
  only after graduation
- The dry run comes **first**, so the one 👉 question quotes a measured size
  rather than an estimate, and an invocation that already names a profile still
  runs it and still asks: the size and the exclusions are what the bootcamper is
  consenting to, and an argument is not consent for what leaves in the archive.
  ⛔ The plugin writes the archive and stops — it never uploads, emails or
  attaches it (INV-135), the same rule the feedback flow follows
- The archive extracts into **one top-level directory**, carries an
  `OPEN_ME_FIRST.md` naming the business problem and what was left out **and
  why**, and a `PACKAGE_MANIFEST.json` recording profile, plugin version,
  `modules_completed`, every included path with SHA-256 and size, and every path
  skipped — a recipient must be able to tell what is **missing** without guessing
- Every member is content-scanned regardless of extension, a member that cannot
  be read is excluded and named, symlinks are resolved and anything landing
  outside the project root is skipped, and `testzip()` re-opens the finished
  archive and writes a `.sha256` sidecar **before** the success line prints —
  never tell a bootcamper an archive exists without that, since they may not
  discover otherwise until they are on the machine that no longer has the
  original
- `scripts/secret_patterns.py` is the one definition of what counts as a secret
  in a file the plugin handles (INV-109): a PEM private key, an AWS access-key ID
  and a Senzing license payload. The write gate blocks a *write* that matches;
  the packager excludes a *member* that matches, so a pattern added for one is
  available to the other. The gate keeps its own inline copy on purpose — an
  `ImportError` in a `PreToolUse` control does not degrade to "no secret scan",
  it degrades to no writes at all — and a test pins the two equal
- `graduation/database-backup.md` is the single implementation of "back up the
  resolved repository", cited by graduation Step 6a and by the `transfer`
  profile. The indeterminate-`database_type` branch is subtle enough that a
  second copy means either no backup or `pg_dump` aimed at a SQLite file
- The visualization server builds from the **export stream** when `--records` is
  omitted: one pass over every resolved entity instead of one
  `get_entity_by_record_id` per record. Module 7 now requires it for a
  bootcamper's own datastore, because the per-record build costs a round trip per
  record — 19,584 of them for one page, against ~15 seconds for the same model —
  and is also *incomplete* there, since an embedded-master record the mapper
  emitted into no input file is invisible to it. An export row carries the shape
  a `get_entity` response does, so both paths share one absorb step
- The graph exposes a **settled signal** — `data-graph-settled` on the document
  element — and a capture-oriented render behind `?capture=1`, so a screenshot
  waits on the layout's own definition of finished instead of a time budget. A
  DOM attribute rather than a JS global, because the simulation is a top-level
  `let` that never reaches `window` and nothing outside the script could observe
  it. The attribute is *removed* rather than set to `"0"`, keeping "not settled"
  and "no animated view here" the same observable state
- The capture manifest records `settled` per tab, with `unsettled` and `unknown`
  kept distinct: reporting a backend that cannot read the DOM as an unsettled
  layout blames the artifact for the instrument
- `capture_screenshots.py` refuses to capture a live server that does not
  implement `?tab=` deep-linking, before any image is written, and deletes
  byte-identical captures as defense in depth behind it. Two tabs cannot render
  the same image; identity means activation did not take, so at most one file
  shows the tab it is named for and nothing on that side can say which
- Graduation's tab-coverage check gained the denominator that does not come from
  the manifests (INV-271): `--check` derives the **expected** visualizations from
  `modules_completed` in `config/bootcamp_progress.json` and names any with no
  manifest, withholding the coverage figure entirely rather than reporting a
  percentage beside an unmeasured visualization
- The graph endpoint carries an `encoding_check` that counts distinct sorted
  source-set keys for comparison against the legend's color keys (INV-259), and
  reports `not_exercised` — never `ok` — below two distinct keys, which is
  precisely why the single-source Truth Set could not catch the defect it is for
- Module 4's License Key gate **re-measures** the license (Step 8a.1) and treats
  that reading as authoritative; SDK setup's Step 5a reading is marked
  provisional via `license_record_limit_measured_at`, because `get_license()`
  resolves what is installed at the time it is called (INV-295)
- The business problem document quotes the bootcamper's **own words** in
  `> "…"` lines beside the guide's rendering, for the five sections built from
  interview prose — and omits the line entirely where the answer was a selection
  from a fixed vocabulary, since an invented "verbatim" line is worse than none
  because it looks like evidence (INV-275)
- Feedback records `submission blocked: <reason>` for a *consented* send a
  no-send session was forbidden to make — never `offered, declined`, which
  states that the bootcamper refused when they agreed, and is the one value that
  reads as "considered and rejected" to anyone deciding later whether the finding
  is still owed upstream (INV-281)

### Changed in 0.5.3

- When proceeding requires **loading** anything — invoking a skill, reading a
  file, running a script — the acknowledgment goes out first, in its own visible
  line, before the first tool call (INV-272). Those calls produce no
  bootcamper-visible output, so an acknowledgment composed afterwards leaves an
  answer looking unregistered for several consecutive calls. Recorded 2026-08-25:
  a bootcamper answered the Module 7 transition question, saw nothing across a
  skill invocation and two file reads, interrupted and answered again — so the
  question was effectively asked twice, which INV-006 forbids, without the guide
  ever re-asking it
- The sourcing checklist is a floor, not the exhaustive set of claims needing a
  source (INV-273): an assertion about **Senzing the company** — its licensing,
  support, pricing, internal process, how its customers or employees do things —
  is subject to the same discipline as an SDK method name. Anything unsourceable
  is labeled as an inference at the point it is made or it is not said, and at a
  gate the bootcamper has already answered the correct action is silence
- The bootcamper's identifying context is for identification and for fields a
  tool requires, never a premise for reasoning about what they should choose
  (INV-274). Employer, affiliation, seniority and entitlement are not inferred
  from an email domain
- A declared MCP schema is authoritative for the parameters a tool **accepts**,
  not for prose describing what it **covers**; for coverage `get_capabilities`
  governs, and a disagreement is settled by making the call (INV-280). Stated as
  a property rather than as its example, since the `find_examples` case it was
  written from was resolved upstream on server 1.36.0
- Module 0's six `search_docs` queries were each measured against the live index
  rather than composed (server 1.35.3, docs index 2026-09-01), and two were wrong
  when checked: a plausible five-stage pipeline query reached none of those
  stages, and an ambiguous-match query returned three Entity-Centric-Learning
  chunks and no ambiguous-match material. `search_docs` is BM25, so a
  plausible-sounding phrase is evidence of nothing — run an entry before changing
  it
- Module 1 sizes a generated scenario to about 10,000 records unless the
  bootcamper asks for more, and states the cost in one line before generating it
  (INV-277). No wall-clock figure and no records-per-second rate: load time
  depends on the workstation, the database and the language, none of which that
  module knows. The ceiling is about how long the bootcamp takes, never about
  license capacity, which nothing has measured yet
- CORD is disclosed as **real data** in the turn that binds it in Module 1, not
  several modules later at Module 4 (INV-293) — a statement, never a gate, since
  it asks nothing the bootcamper can act on
- Feedback asks only the questions the triggering message has not already
  answered (INV-006). The partial message is the normal shape: someone who stops
  to report a defect usually names the subject, what happened and often a fix,
  and re-asking those three spends goodwill they are already spending
- Module 6 reads the tier **and** `database_type` when sizing loader workers —
  the tier picks the pattern, the engine picks the worker count (INV-296) — and
  says plainly that proceeding on SQLite keeps the serialized writer count
- Module 5 prints a per-`RECORD_TYPE` presence breakdown before reporting a
  quality score, as a precondition rather than another heuristic (INV-174,
  INV-264), and discloses deliberate gaps on a `provenance: synthesized` source
  before the gate rather than after it
- Module 6's validation routes match accuracy on the **per-record** bucket only
  (INV-264). The per-record and relationship buckets mean opposite things, so a
  relationship-bucket share is not evidence of poor match accuracy, and pooling
  them produces a figure about neither
- Node labels live in their own layer appended after the node group, are
  truncated to 20 characters with a collision suffix, and the collision force
  accounts for label extent — but only while labels are actually drawn, so the
  production-scale layout is not over-separated for text nobody renders
- The README says the plugin works wherever Claude Code runs and names the web
  app and IDE extensions, while stating plainly that the walkthrough has verified
  install steps only for Claude Desktop and the CLI. The command table lists all
  five commands, including `/bootcamp-note` and `/package-bootcamp`

### Fixed in 0.5.3

- **A stray fence marker deleted finalized modules from the recap.** Both fence
  handlers located their terminator with the next end marker *anywhere* in the
  document, so an unterminated start annexed the region up to a later fence's
  terminator. Measured 2026-09-01 on the shipped script: a three-module recap
  parsed to two, with `## SDK setup` and its content gone. It was silent three
  ways — `audit_recap` fired the unfinalized-module warning, which is true and
  about something else; `--expect-modules` checks presence and never absence; and
  the retention count stripped the same region, so the deleted module left the
  **denominator** too and the run reported 94% retention with no fatal. A fence's
  span now never extends past the next start of its own type, a stray is skipped
  rather than paired, and `audit_recap` names it by offset
- A resumed session put five phantom "modules" beside the real ones in the
  keepsake PDF: `recap_checkpoint.md`'s interior uses `## ` headings, the
  durability hooks fold it verbatim, and every `## ` in a recap is parsed as a
  module. The checkpoint fence is lifted before module parsing now, through a
  `DISCARDED_FENCES` tuple the parse path iterates rather than markers named one
  at a time, so a third fenced block cannot repeat this by being overlooked. The
  lift is refused where it would empty a recap that had module headings —
  phantom sections that `audit_recap` warns about are the lesser loss against
  deleting real content
- The same checkpoint block was counted in the retention denominator, where a
  resumed session's checkpoint is large relative to a partly-written recap:
  42% retention, fatal, with the lift's own effect measured as content loss and
  the PDF blocked outright against INV-048
- **A headless capture ran 5 of the ~300 ticks the layout needs**, at every
  virtual-time budget from 5s to 300s, because d3's timer is driven by
  `requestAnimationFrame` and headless virtual time does not advance it. The
  nodes sat near their initial phyllotaxis positions, which look plausibly spread
  out — which is why it went unnoticed. The capture render now stops the
  simulation, ticks it to completion synchronously and places once. Measured
  2026-09-03 on the 85-entity Truth Set: five captures at 30s and five at 120s
  produced the same image while five at 300s produced two, so the deadline never
  selected the layout and lengthening it made reproducibility worse
- A settled layout is not automatically a visible one: `forceCenter` centers the
  centroid and bounds nothing, so the finished 85-entity layout spread well
  outside 1440x900 and presettling alone put most nodes off-canvas — losing more
  of the graph than the unsettled clump it replaced. The capture now fits the
  layout to the viewport extent, and drops labels above 40 nodes, since a 10px
  label at the fit scale is 2–3px at 85 entities. The interactive view is
  untouched; a real browser advances animation frames normally
- Node labels were text children of each per-datum node group, so paint order put
  a later circle over an earlier label. Observed at N=2, the smallest possible
  graph: "Aurelia B Quorndon" rendered as "relia B Quorndon" with the leading
  "Au" behind the neighboring circle, byte-identical across an 8s and a 30s
  budget, so it was the settled state and not a settling artifact
- `--single` did not request the capture render, three lines above the two
  per-tab branches that did — a rule applied where the defect was measured rather
  than everywhere it binds (INV-246). Every capture path goes through one helper
  now
- **Tab coverage reported a clean pass on a bootcamp that had captured nothing
  from its own data.** On a 2026-08-25 run `--check` reported "6 of 6 captured
  tabs reached the recap" while the entire Module 7 application, built over the
  bootcamper's own resolved data, had not been captured at all: the check's
  denominator is the manifests that exist, and a module that captured nothing
  contributes none. The recap PDF illustrated the bootcamp with six pictures of
  the demo Truth Set and the bootcamper's cross-source entities and fraud leads
  appeared only as prose. The sentence was true of the manifests and false of the
  bootcamp
- A live server implementing every tab id, section id and nav id but no `?tab=`
  deep-linking served its **default tab** for every request, and every earlier
  check passed because the ids it looks for were all present. Measured 2026-08-28
  against a Java server built to the contract: six files, five distinct images,
  two byte-identical, exit 0, with the images reaching the recap captioned as tabs
  they do not show
- The legend's per-source counts were labeled "Single-source" while the rows were
  **participation**-shaped throughout. On a two-source run it read
  `CRM_CUSTOMERS 65` / `WEBSTORE_ACCOUNTS 70` against 121 entities with 14
  spanning both; the true single-source figures were 51 and 56, and nothing on
  screen contradicted the misreading because each figure agreed with every other
  total in the app. Relabeled rather than recomputed — the tooltip filters the
  source, the click handler keeps a node when any of its sources is on, and the
  swatch is the per-source color, so changing the counts would put the label in
  agreement with the heading and out of agreement with all three
- That row sat inside the cross-source branch and vanished on single-source runs,
  where the label happens to be correct — hiding the defect from the simple case
  and showing it only on the runs the module exists to demonstrate
- The entity-size histogram's y-axis counts entities, which are whole, but
  `.ticks(n)` labeled a `[0,1]` domain in fifths — "0.4 entities". Small maxima
  are the normal bootcamp shape, since the built-in evaluation license caps
  ingestion at 500 DSRs. Integer tick **values** are chosen now; `.tickFormat("d")`
  alone rounds the labels while leaving the fractional positions and yields
  duplicates
- The packager had a file-type allowlist carrying `.md`, `.py` and `.json` and
  not `.pem`, `.key` or the empty extension, so a `server.pem` and an
  extensionless `id_rsa` were packaged while the same key in a `.py` was
  excluded. There is no allowlist now — every member is scanned by content
- A recap or discoveries document that exists but is not UTF-8 — an editor
  saving cp1252 is the usual cause — produced a traceback. Both generators refuse
  with a message the guide can relay and write no PDF, rather than re-reading
  with `errors="replace"`: the document is the bootcamper's, and silently
  mangling their text is not the plugin's to do
- `license_record_limit` could be written from a number the bootcamper *stated*.
  On 2026-08-25 a stated 100,000 was written against an install whose measured
  limit was **500**, on a ~94,000-record scenario — and the failure is a
  *suppressed* warning rather than a wrong number, since a limit above the dataset
  size suppresses Module 4's volume gate, the single volume-gated prompt in the
  bootcamp. The field is written only from a measurement now, and a stated
  entitlement is recorded as `license_stated_limit` in the preferences file, in a
  different file from the measured value so proximity cannot confuse the two
  (INV-244, INV-278)
- A bootcamper said their **possible**-fraud entities should feed the fraud tool;
  the document rendered it as "Internal fraud tool (**confirmed fraud cases**)" —
  one adjective, and a different routing rule. The same document still said
  "Possible-fraud entities routed to the internal fraud tool" four lines earlier,
  so it contradicted itself and carried nothing that could settle which reading
  was right. It was confirmed as accurate at Step 15 and propagated: Module 7's
  requirement 7 came out titled "Confirmed-fraud candidate list", and was approved
  too — reviewing, again, only the refined artifact
- Desired Output recorded a single value where Step 6d is a multi-select, so "1
  and 3" was written as `Master list` alone. That narrowing does not stop at the
  document: Module 7 derives its query requirements from it (INV-286)
- The capture helper's two non-zero exits need opposite responses and were being
  read as one "it failed": exit **1** is an unrecognized tab id, rejected before
  anything was captured, and taking the skip path there drops **every** screenshot
  from the recap while all of them were available. Exit **2** is a capability or
  content limit, and two of its three reasons are not about a missing install —
  a Windows machine carrying both Edge and Chrome was once told no capability was
  available, sending the reader to install software they already had
- The single-page safety net and the deep-linking guard were ordered wrongly:
  above the net, the guard read `tabs == []` for a page with no tabs and refused a
  single-page deliverable that was never going to select a tab. Measured
  2026-08-31: rc 0 with an image before, rc 1 with none after
- Module 2 carried `${SENZING_ROOT}` to Linux, where the macOS install sets it
  and nothing on Linux does (INV-283), and reported that a binding loaded without
  reporting **where it resolved from** — a locally installed package shadowing the
  SDK produces a working import and the wrong code (INV-269). The path is printed
  beside the version now, not a package-metadata version, which reports the wrong
  package

## [0.5.2] - 2026-08-24

### Added in 0.5.2

- **Bootcamper notes.** A `/bootcamp-note` command, a `notes.md` workflow in
  `bootcamp-onboarding`, and a `UserPromptSubmit` route for "make a note", "note
  to self", "jot this down", "remind me" and "add a to-do" append the
  bootcamper's own ideas, questions, reminders and to-dos to
  `docs/bootcamp_notes.md`, in their words, with the time, module and pending
  question captured silently. A note is never routed, never triaged and never
  sent anywhere; when a message matches both vocabularies ("make a note that the
  bootcamp is broken") **feedback** wins, because naming the bootcamp as the
  thing at fault makes it an attributed defect report rather than a private memo
- Graduation folds those notes into the recap inside a
  `<!-- BOOTCAMP-NOTES -->` fence and both PDF renderers give them their own
  page and contents row. The **fence** is the discriminator, not the heading
  text: the block is lifted out before module parsing, so a note can never be
  promoted to a module section and cited on the Certificate of Completion. The
  bootcamp's own elaboration and the machine-composed context are rendered under
  their own labels, never merged into the bootcamper's words, and the notes are
  counted toward content retention — otherwise writing enough of them would push
  a recap under the retention floor and make the generator refuse to render it
- The structural recap check (`generate_recap_pdf.py --check`) now runs after
  **every** module's append, not only at graduation. It catches a subsection
  written as a bold label rather than an `###` heading — indistinguishable in any
  Markdown viewer, and it drops the whole module from the PDF. One run wrote bold
  labels at its first module, reproduced them for all nine, and discovered it at
  graduation as "0 of 9 '##' sections carry any recognized sub-section"
- `--check` also reports a tab manifest that records fewer captures than there
  are PNGs beside it, so the coverage check can no longer pass on a denominator
  that is too small
- The visualization server mints a per-process nonce, exposes it on
  `/api/stats`, and confirms **it** is the server answering the port before the
  URL is handed over; the any-language contract requires the same of a
  bootcamper-written server. The nonce is compared rather than the record count,
  because two runs of the same project agree on the count — exactly the case
  where the stale listener is the bootcamper's own earlier server
- The graph endpoint caps the nodes it emits and carries `total` and `capped`,
  ranking candidates by source span, then connectivity, then deterministically,
  so a re-rendered snapshot cannot disagree with the recap describing it
- Ground rules gain "Running a file you just wrote, when the run happens
  somewhere else": on the `docker` path a parse error in a just-written file is
  retried **once** and confirmed with an in-container compile before it is
  believed, because a partially-synced read reports an error at a well-formed
  line and a host/container version story is always available and usually wrong
- WSL2 is named as the second Linux-environment route for Python on Windows —
  the server's own second option, which the language gate never offered — and
  bootcamp preparation states its cost (a system-level install, administrator
  rights, and a reboot) at the point the choice is still free
- Module 2 measures the datastore before creating it when the project sits on a
  mounted host filesystem (WSL2 under `/mnt/`, a Docker bind mount), reporting
  `check_repository_performance` rather than asserting a rule, and leaves the
  relocation decision to the bootcamper. Observed on one workstation: 1,112
  inserts against 326,606, and load throughput of 3 records/second against
  138-180
- Module 4 states the completeness target as a band with the arithmetic that
  reaches it — no single field can move a seven-field source into 70-79% at any
  absence rate — and verifies the generated data against that band before the
  module closes, widening the gaps and regenerating rather than recording a score
  it did not measure
- Phase B reconciles a loaded count three ways instead of two: equal, an
  **explained delta** that a named mapping artifact predicts, or unexplained. The
  explained branch is reachable only with a citation. `embedded_master` — a
  disposition the bootcamp teaches — necessarily makes the loaded count exceed
  the input count, so a completely successful load (3,727 loaded from 3,488
  records, 239 embedded masters, zero errors) had to be filed as `failed` under
  the two-way rule, and written into the bootcamper's own loading strategy
- Module 5 labels every cross-source pair in the evaluation report `measured` or
  `candidate, overlap unmeasured`, and requires a distinct-value overlap count on
  the named attribute for the former. Two sources at IDENTIFIER 100% were once
  written up as the highest-confidence pair "both carrying LEI" when one carried
  2,375 LEI values, the other one, and a single value was shared in the whole
  dataset — the group scores were correct and the inference was wrong by ~38x
- Module 5 checks a `disposition: payload` field's emitted key against the
  registered feature attributes at the plan gate and offers a `_PAYLOAD` rename,
  rather than letting the bootcamper's explicit "do not match on this" be honored
  in form and not in effect
- A sent license request is recorded as an event (`license_key_requested`) with
  its channel and date, and Phase B's load decision reads it to say the license
  may already have arrived by email — only when a request is actually
  outstanding, since `license: evaluation` is also written when the bootcamper
  declined to send one

### Changed in 0.5.2

- Every MCP citation, negative and positive, was re-verified against server
  1.33.0 (2026-08-21 and 2026-08-23) and carries that version and date
- Module 1's scenario invariant asks for **cross-source mapping divergence** —
  two sources describing the same feature in different shapes — instead of "at
  least one transformation". A joined name is a direct mapping to `NAME_FULL`
  under the Entity Specification, so a scenario built to satisfy the old wording
  satisfied nothing and encoded a plan the specification does not call for
- The Entity Specification's `NAME_FULL` rule is stated where mapping happens
  and routed to from Modules 1 and 4: a single name field is a direct mapping,
  however parseable it looks. One run recorded two such columns as "needing
  splitting" in two documents a module before anything could check it
- The reason for sending `mapping_workflow`'s step-1 `profile_summary` as an
  array is now that the **schema declares** it, not that the prose form fails —
  server 1.33.0 accepts both shapes, so a caution phrased as an outcome had
  already expired while one phrased as a contract had not
- The profile report has two possible filenames — `profile_report.md` for a
  single-file start, one `profile_report_<stem>.md` per input otherwise — and the
  relocation rule is now "no profile report is left in the shared workspace,
  whatever the server named it"
- Module 7's Poor possible-match band is a **finding** with three outcomes rather
  than a verdict on the mapping, routed by the server's own concentration test
  and a comparison against the profiler's uniqueness stats. On the
  generated-scenario path the plugin creates the gapped contact fields that
  produce near-misses no remap can fix
- `SZ_INCLUDE_MATCH_KEY_DETAILS` is passed on why calls again, with a relations
  flag. The directive that forbade it rested on a measurement whose two arms
  **both** passed the flag, so its contribution was never varied; following that
  directive produced a why demonstration with no match-key breakdown, reading as
  "this SDK doesn't provide that detail"
- Container teardown uses the two things a Debian slim image is guaranteed to
  have — the shell's `kill` builtin and the `python3` the SDK install brings
  in — because `procps` and `lsof` are absent and are deliberately not added to
  the bootcamper's image. The port's answer, never the kill's exit status, is the
  exit condition
- Module 6 Phase C merges the dependency and strategy confirmations into one
  question on the generated-scenario path, and Phase D self-directs the UAT
  rather than asking a bootcamper to convene business users for a business case
  the bootcamp invented — two consecutive rubber stamps in the first case, an
  unanswerable question in the second
- `docs/bootcamp_notes.md` is excluded from the `production/` handoff alongside
  the recap and the feedback directory: it holds the bootcamper's notes about
  learning the tool, not project content
- Graduation exempts the generated-scenario marker's robot face from the
  unrenderable-character tally — the marker is read from the Markdown by four
  files and by nothing in the PDF — and nothing else
- Spelling normalized to American English across the scripts and skills, in
  code comments and prose alike — `color`, `gray`, `centered`, `labeled`,
  `judgment`

### Fixed in 0.5.2

- **Not one hook had ever run.** `args` is not part of the `type: command` hook
  schema, so all six hooks launched a bare `python3`, which read the event
  payload as its program. Each hook is now a single `command` string naming the
  interpreter and the script with the plugin root **quoted**, so a root
  containing a space still works — and the README section claiming exec form
  spawned the interpreter shell-free on every platform is corrected in place
  rather than quietly deleted
- A targeted re-capture rewrote the tab manifest from scratch, leaving
  `captured_count: 1` where six tabs had been captured; coverage then reported
  full coverage on a 1-of-1 denominator, and would have done so just as
  cheerfully with five of the six images lost. `write_manifest` merges per tab,
  keeping every entry for a tab this run did not touch
- The visualization server bound the wildcard address, which does **not** collide
  with an existing loopback listener: both binds succeed, two processes listen,
  and either may answer. Observed on macOS with a three-week-old server from an
  unrelated project holding `127.0.0.1:8080` — the other outcome shows the
  bootcamper a stranger's dataset under their own project's title, with the
  keepsake screenshots capturing it
- Graph nodes were colored by `data_sources[0]`, so 1,951 cross-source entities
  rendered in the single-source `GLEIF` color under a legend implying they were
  GLEIF-only — the bootcamp's headline result invisible in the tab built to show
  it. A node is colored by its whole source set, fill, stroke and width alike;
  the palette is allocated in one pass over sources **and** combinations, since
  two passes each restart at the top and reproduce the collision; and the legend
  names every combination it draws
- `. env.sh && python3 server &` backgrounds a **subshell**, so `$!` recorded a
  pid two away from the server: the kill exited 0, the subshell disappeared, and
  the port stayed bound by the still-running server. The env is sourced as its
  own statement, and teardown falls back to the port whenever the pid does not
  stop the server — a wrong pid presents as presence, which is why it was worse
  than a missing one
- The Truth Set tab-set comparison matched `data-tab="…"`, which appears nowhere
  in the generated app: it found zero identifiers on both sides and reported "tab
  sets match: True". It matches `id="tab-<name>"` and asserts a non-zero count
  before comparing
- Screenshot capture was skipped on an assumption that headless automation was
  unavailable, losing twelve recap images — permanently in Module 3b, which
  purges its records at close. The bundled helper is run and its **exit code**
  decides, since it distinguishes "no headless capability" from "no requested tab
  exists"; the same script then captured 6 of 6 tabs first try
- `sdk_guide(topic='configure', data_sources=[…])` selects the registration
  snippet and substitutes **nothing** — the returned code still carries the
  sample tuple `("CUSTOMERS", "REFERENCE", "WATCHLIST")`. Shipping it
  unsubstituted registers three codes the bootcamper does not have and leaves
  the first load failing `SENZ2207` on the codes they do
- Data-source registration is re-runnable by construction rather than by
  catching an error: an identical configuration returns the existing config ID,
  so idempotency arrives one call after the per-code registration. No route
  documents a raised error for re-registering a code in any binding, and
  `search_docs(category='sdk')` indexes community wrappers whose error contracts
  are not the official binding's
- Why responses were parsed for `MATCH_KEY`, `ERRULE_CODE` and
  `MATCH_KEY_DETAILS` — the **entity-side** names, real but carried on
  `RESOLVED_ENTITY.RECORDS[]`. The rename is the whole family: `WHY_KEY`,
  `WHY_ERRULE_CODE`, `WHY_KEY_DETAILS`, and all three render blank rather than
  raising
- A `find_network` response carries **two** endpoint conventions at once: paths
  are `START_`/`END_` (directed) and links are `MIN_`/`MAX_` (undirected,
  normalized low-to-high). Reading path names off a link element printed all 38
  edges of a corporate hierarchy as `null -> null`, with no error — and an empty
  edge list is indistinguishable from "this data has no relationships"
- A `how_entity` step's two sides are the **objects** `VIRTUAL_ENTITY_1` and
  `VIRTUAL_ENTITY_2`; `INBOUND_VIRTUAL_ENTITY_ID` is a string ID and no
  `CANDIDATE_VIRTUAL_ENTITY` exists at any depth. The `INBOUND_`/`CANDIDATE_`
  pairing is real one level deeper, which is why the wrong key survives a
  name-level lookup and renders every step blank
- `CONFIRMATIONS[]` has a third state — present and empty — which is a data and
  rule outcome, not a missing flag: fall back to `FEATURE_SCORES` and say so,
  rather than adding flags or rendering an empty section
- Phase A improvised a load menu offering "wait until the evaluation license is
  applied" with no way to reach it. The load decision, its pinned question and
  the pointer to the one apply procedure belong to Phase B, once
- Module 1's license comparison took the **requestable** evaluation license's
  capacity — described in `submit_feedback`'s own tool description — for the
  built-in one, so the comparison passed when it should have failed,
  `license_guidance_deferred` was left unset, and Module 4's gate never fired.
  The figure comes from `sdk_guide(topic='load', record_count=<above the limit>)`
- Module 4 sized a generated dataset down from 538 records to 466 to stay under
  a limit it had not measured, reasoning that an absent `license_record_limit`
  meant no custom license; the workstation carried an EVAL license with `recordLimit:
  0`. An absent value means **never measured**, and on no cap the scenario's own
  needs decide the size

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
