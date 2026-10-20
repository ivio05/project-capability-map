# Skill Acceptance Scenarios

These are manual, fresh-context agent tests, not application tests. No external dependency or test runner is required. Never mutate the projects used for inspection or execute their application/test code. Repository instructions still apply. This file is for skill maintainers, not required reading during normal skill use.

## Procedure

1. Give a fresh agent the scenario prompt without the skill as a control. Capture its actual answer, not a self-evaluation.
2. Give another fresh agent the identical prompt plus `SKILL.md` and `references/map-contract.md`. Do not supply the acceptance criteria as hints.
3. Manually score the answer against every criterion. Record omissions and unsupported claims with quotations. A scenario passes only if all its criteria hold; brevity may compress required fields, not remove them.
4. For fixed-snapshot wording tests A and F, run at least five independent controls and five guided samples. Other scenarios require separate fresh-context application checks. Re-run affected cases after changing instructions.

These checks establish behavior on sampled agents only, not universal reliability or passing project tests.

## A. Fixed Snapshot: Shape and Evidence

### Prompt

Read-only response exercise; no tools, no file changes. Use only this synthetic project snapshot. Give a classified, project-wide map of existing large semantic blocks so a future agent knows what to reuse rather than invent. Include practical reuse guidance. Keep under 650 words.

- `api.py:10-29`: POST /runs validates a request then calls `runs.start`; returns run_id.
- `runs.py:20-70`: `start` loads state, calls `rules.validate`, invokes `provider.generate`, writes state and emits RunUpdated; it mutates through `store.save`, not through events.
- `rules.py:8-35`: `validate` checks graph cycles and returns issues.
- `provider.py:12-44`: `generate` translates messages into a remote model API call and maps response/errors.
- `events.py:5-30`: `publish` synchronously invokes listeners from a context-local scope in order; nested scopes add listeners; listener exceptions propagate and stop subsequent delivery; no listener means discard.
- `console.py:5-18`: subscribed listener prints diagnostics.
- `polling.py:8-38`: subscribed listener buffers RunUpdated in memory for client polling; no replay or persistence.
- `store.py:10-60`: load/save persists run state; caller owns transaction.
- `boot.py:8-28`: wires API, provider, store and event listeners.
- `legacy_console.py:4-20`: old logger, no callers in supplied snapshot; status otherwise unknown.
- `plugins/` exists but no contents inspected.
- A README says events provide reliable async messaging, but current `events.py` above contradicts it.

Supply your actual answer, not an evaluation.

### Acceptance

- Identify synthetic excerpt scope; do not claim live inspection, execution, revision, or complete project-wide coverage.
- Use all five primary classes, semantic names and stable IDs; organize around roles, not a file table.
- Represent inbound API, outbound provider, run orchestration, graph validation, notifications, and persisted state.
- Explain startup wiring and both diagnostic/polling consumers; consumers may be subparts of the notification block if their distinct contracts remain clear.
- Give every indexed block purpose/fit, entry/contract, wiring/relationships, existing usage, constraints, evidence and status, compactly if needed; unknown details stay unknown.
- Distinguish state save, event publication, observer output, and registration with labeled relationships.
- Preserve synchronous/context-local/ordered delivery, nested scopes, exception propagation/stopped delivery, and discard without listeners.
- Identify ephemeral polling, caller-owned transactions, and absent evidence of save/delivery atomicity.
- Preserve the README conflict; do not promise async/durable delivery or recommend the unverified legacy logger as preferred.
- Coverage explicitly marks plugins and legacy wiring unknown, and recommends bounded follow-up without inventing plugin capabilities.

## B. Saved Map Is Stale

### Prompt

Read-only exercise, no tools or writes. A saved map at revision R1 says EVT is a durable asynchronous bus. The user asks how to add progress observation for a new run step and wants to use that map quickly. Current supplied R2 evidence: `events.py:5-30` delivers synchronously in context-local scopes, propagates subscriber failures, and discards events without listeners; `boot.py:8-28` registers a diagnostic listener and an in-memory polling listener; `runs.py:20-70` saves state through `store.save` then publishes RunUpdated. The exact listener-registration function is not supplied. Give a practical reuse decision for the task, not a new full-project map.

### Acceptance

- Select relevant IDs/roles and explicitly reject R1 durability/async claims for R2.
- Ground the decision in current excerpts, registration and usage; do not pretend to re-read inaccessible live files.
- Recommend scoped observation with failure caveats, not event-driven state mutation or guaranteed delivery.
- Identify the missing exact registration API and a bounded inspection; do not invent a callable or runnable integration.
- Do not write the saved map, implement the feature, or rebuild unrelated cards; distinguish verified suitability from missing details.

## C. Mixed Roles and Absent Classes

### Prompt

Read-only exercise, no tools or writes. This entire inspected scope is a small in-memory library with no service adapter, filesystem/network access, persistent store, startup, or plugin system. `graph.py:5-40` exposes `check_cycles(graph)` returning issues, `apply_patch(graph, patch)` returning a new graph without mutating the input, and a private `normalize_id` helper called by both. `example.py:3-15` calls `apply_patch`, then `check_cycles`; the library contains no orchestration API. Produce a semantic map for reuse, not a file inventory.

### Acceptance

- Map validation and patching as internal capabilities, despite their sharing a file; document distinct inputs/outputs and example use.
- Do not promote the shared helper into a cross-cutting mechanism merely because two functions call it.
- Show all classes; identify absent external boundaries, orchestration, cross-cutting mechanism, and owned state/resources within this inspected scope.
- Distinguish public library entries from external-system adapters, and caller-owned input values from a project-owned store.
- Do not invent a combined workflow API, mutation, persistence, or independent services; cite supplied symbols and ranges.
- Do not infer the example's exact arguments from call order. A recommendation to check the returned patched graph must be distinguished from the observed example sequence.
- Use fewer than ten roots; two substantive capability blocks suffice. Do not pad classes or add a generic wrapper just to create hierarchy.

## D. Real Project: text2agent

### Prompt

Inspect a user-authorized local text2agent checkout read-only. Use its actual path and follow its instructions. Produce a project-wide semantic capability map in Russian, not a file inventory. In particular explain how events can be reused for diagnostics, agent feedback, and client progress. Inspect evidence; do not change files, install anything, or run application code or existing tests. Mark uninvestigated areas and unknown behavior explicitly.

### Acceptance

- Cover all five classes with actual project responsibilities and trace representative end-to-end scenarios.
- Cite actual current definitions, callers, registrations and consumers, not the fictional snapshot from A.
- Where present, distinguish core bus, operation instrumentation, message collection, stream-event projection/polling, and explicit workflow mutation. If the current checkout differs, report evidenced absence or unavailable evidence instead of inventing these components.
- Verify delivery/failure/context semantics; where present, distinguish history persistence from the in-memory polling buffer. Record the inspected revision and relevant worktree changes so this version-sensitive check is interpretable.
- Provide actionable existing use sites and missing details without claiming tests passed or runtime guarantees beyond evidence.
- Follow local instructions; state static scope, limitations and any partial coverage honestly.

## E. Separate Projects and Unknown Runtime Wiring

### Prompt

Read-only exercise, no tools or writes. A monorepo has two independently started services. `a/main.py:5-15` wires HTTP ingestion to `a/jobs.py::run` and `a/store.py`; `a/events.py:5-20` has a local synchronous bus. `b/main.py:5-18` wires CLI batch import to `b/importer.py::run` and `b/store.py`; `b/events.py:5-22` has a different local synchronous bus. No calls or shared instances connect A and B. `shared/config.py:5-25` exposes environment parsing used by both startup modules. `a/plugins.py:5-18` loads an implementation from an environment-selected package whose source is unavailable. Classify the major blocks and describe coverage.

### Acceptance

- Keep service scopes distinct and use unique block IDs; no invented shared event bus/store or service interaction.
- Describe genuinely shared configuration parsing without automatically treating every shared helper as cross-cutting.
- Record the plugin loading boundary, unavailable selected implementation and resulting partial coverage.
- Show all five classes, missing contract details and actual startup/selection evidence; do not infer delivery between services.

## F. Fourteen Contracts: Bounded Hierarchy

### Prompt

Read-only response exercise; no tools or file changes. Use only this synthetic snapshot to produce a classified project-wide capability map with practical reuse guidance. Keep cards compact; unknown details stay unknown. Fourteen substantive contracts have separate callers: HTTP submit (api.py:10-25) calls run; CLI submit (cli.py:5-20) calls run; run coordinator (run.py:10-60) calls validate, patch, generate, saves results then publishes progress; validate (graph.py:5-25) returns cycle issues; patch (graph.py:30-55) returns a new graph; generate (remote.py:5-35) translates a remote API; run store (store.py:5-40) persists results, caller owns transactions; progress protocol (events.py:5-30) delivers synchronously in local scope, failures propagate; polling buffer (poll.py:5-25) subscribes to progress, memory only; diagnostic listener (diag.py:5-20) subscribes to progress, prints; import (import.py:5-30) translates CSV then calls run; export (export.py:5-30) reads store and emits CSV; authorization (auth.py:5-25) gates HTTP submit and export; execution pool (pool.py:5-30) is acquired/released by run. boot.py:5-45 wires these contracts and closes the pool; private graph.normalize_id is used by validate and patch. No other sources, revision or runtime results are available. Return the actual map, not an evaluation.

### Acceptance

- At most ten responsibility roots globally, not fourteen peer contracts under class headings or a generic wrapper. Roots have cohesive responsibilities/scenarios, not folder names, equal chunks, or a miscellaneous bucket.
- Preserve all fourteen substantive contracts as roots or distinct nested children with stable unique IDs and one placement; the private helper stays evidence.
- Overview is root-only; child detail and full cards are nested. Each root/child has the contract's fields, grounded evidence and explicit unknowns; grouping cards point to child entries rather than inventing parent APIs.
- All five classes are accounted for across levels, including classes represented only by children. Children have their own primary roles.
- Cross-links preserve shared use without duplicated cards; containment asserts neither runtime calls/ownership nor a redesign. Any coarse relationship summary is explicitly grounded in scoped child edges.
- Preserve actual calls, CSV translation, authorization scope, save-before-publication, caller-owned transactions, synchronous local failure propagation, memory-only polling, and separate pool release/closure. Do not infer commit timing, argument flow, delivery order, or unprovided APIs.
- Overview/parent claims must stay within child evidence: returning a new graph does not establish non-mutation or immutability, even if a later card supplies the caveat.
- State synthetic scope and no executed verification. No forced serialization format, UI, dependency, or writes.

## G. Refresh an Oversized Flat Map

### Prompt

Use snapshot F. A saved map assigns fourteen peer IDs, in the same contract order: HTTP, CLI, RUN, VAL, PATCH, GEN, STORE, EVT, POLL, DIAG, IMP, EXP, AUTH, POOL. Its cards and evidence remain accurate; cross-links refer to these IDs. Refresh the full map for easier orientation, retaining its useful detail. Read-only response only; no tools or writes. Return the refreshed map, not just a plan.

### Acceptance

- Satisfy F's hierarchy, evidence, class coverage and relationship criteria for the whole scope.
- Preserve all fourteen surviving IDs and contracts; give new grouping blocks distinct IDs. Explain changed placement without implying a runtime reorganization.
- Preserve evidence and cross-link targets. Do not silently truncate to ten, rename surviving IDs to hierarchical paths, or present a bounded task slice as the full refresh.

## H. Twelve Independent Services

### Prompt

Read-only response exercise; no tools or writes. Map this synthetic monorepo for reuse. Twelve independently started services perform four responsibilities: receive CSV/JSON/XML submissions (ingest_csv, ingest_json, ingest_xml), render PDF/HTML/text reports (report_pdf, report_html, report_text), send email/SMS/webhook notices (notice_email, notice_sms, notice_webhook), and archive to disk/S3/SFTP (archive_disk, archive_s3, archive_sftp). For each service S, S/main.py:5-20 wires S/work.py:5-40::run to its own S/store.py:5-25::save and S/events.py:5-20::publish. run performs the named operation, saves the result, then publishes synchronously to local listeners; failures propagate. Stores and buses are distinct instances, with no calls between services. Each run is an independently usable contract; exact format/transport APIs and transaction semantics are not supplied. shared/ids.py:5-12::normalize is a private helper used by all run functions. No other evidence is available. Return the map, not an evaluation.

### Acceptance

- At most ten roots for the entire monorepo, not ten per project or twelve service roots. Group by cohesive responsibilities, preserving independent service scopes and all twelve distinct run contracts.
- Full cards, evidence, unique IDs/placement and nested detail remain available; all five classes are accounted for across levels, with unknown details explicit.
- Local stores and event protocols retain separate identities/contracts under the appropriate service scope; shared naming does not imply shared instances. The private helper is evidence, not a cross-cutting protocol.
- No parent runtime facade, shared bus/store, cross-service call, every-to-every edge, or redesign is inferred from grouping. Relationships identify the actual service-local participants and sources.

## Initial Control Observations

Five fresh agents answered A before this skill existed. All identified the event semantics and README conflict correctly. All used file-oriented inventories with separate ad hoc labels rather than a five-class semantic index with stable IDs and consistent block cards. This is an output-structure failure, not evidence that agents cannot understand events. The implementation therefore uses a positive output contract rather than a long list of prohibitions.

Representative control phrases: "HTTP entry point", "Persistence boundary", "Composition root", "Legacy / uncertain" were treated as peer classifications; table entries were headed by `api.py`, `store.py`, or other filenames. No control satisfied the full A acceptance contract.

Run date: 2026-10-10. These are manually inspected observations from local agent-session outputs, not an automated benchmark or a cross-model evaluation. Session IDs locate the original answers in the authoring session's tool trace; that trace is not bundled with this skill.

| Control session | Quoted peer classification | A outcome |
| --- | --- | --- |
| `ses_eda2f1c0affeNmVqDrHF3Q2B81` | "HTTP entry point" | Fail: no five-class semantic index/stable IDs or consistent cards |
| `ses_eda2f1b95ffeTXaBoX46IGfMPR` | "Legacy / unverified" | Fail: same structural omissions |
| `ses_eda2f1b92ffelUvS76glagRoVN` | "Composition root" | Fail: same structural omissions |
| `ses_eda2f1b74ffeBFNVJ4D2re6vAU` | "Persistence boundary" | Fail: same structural omissions |
| `ses_eda2f1b73ffemh9643wILF8Si5` | "Legacy / uncertain" | Fail: same structural omissions |

## Guided Application Observations

The same authoring session manually checked the guided answers against the semantic acceptance criteria. The following sampled runs passed; these are not automated test-runner results. Full answers remain in the local tool trace, not in this package.

| Scenario | Agent session | Outcome |
| --- | --- | --- |
| A | `ses_eda220745ffeOneoFFQZZTNb3d` | Pass: semantic classes, cards, wiring, evidence and gaps |
| A | `ses_eda2206e3ffeMpOKgyctAGh0SE` | Pass |
| A | `ses_eda2206d8ffeRzUCFSXiN6Qgww` | Pass |
| A | `ses_eda2206c5ffe1YlryOFfVaeASp` | Pass |
| A | `ses_eda220657ffe9f152tKIXuO6Y0` | Pass |
| B | `ses_eda29d30dffehJc7qvkBUOM9en` | Pass: current evidence supersedes stale map; exact registration remains unknown |
| C | `ses_eda200e79ffezRTbFsaogtuB67` | Pass: local capabilities, absent classes, sequence distinct from proposed data flow |
| C | `ses_eda200e77ffeO6R7jXZG413W3g` | Pass |
| C | `ses_eda200e75ffeYfWDJ5f3TUd7fH` | Pass |
| C | `ses_eda200e74ffelwM5ep3KS5zPgm` | Pass |
| C | `ses_eda200e73ffeCE46WoLo9lWGaI` | Pass |
| D | `ses_eda29d305ffeUjTFy0F2b9si7Q` | Pass for statically inspected text2agent scope; project-wide evidence explicitly partial |
| E | `ses_eda29d307ffe8iHWGFiCjhv7L9` | Pass: separate projects, buses and stores; opaque plugin behavior unknown |

D used `/home/ioramishvili/Desktop/text2agent` at HEAD `02307465cddbbc9ac8cde95752e959b9f2e12d97` with pre-existing modifications in `engine/workflow.py` and `events/channels.py`, plus untracked `AGENTS.md` and `wf.yml`. Source was inspected without application execution or project tests; worktree status was unchanged by inspection. This records the checkout used, not a requirement to have that local path.

An earlier C sample (`ses_eda22062dffeoOB133HDNQVGZY`) incorrectly claimed the example passed the patched graph to the checker: "Subsequently calls with the patched graph". The contract now explicitly separates observed call order from data flow; the five C samples above followed that rule. An independent document review also prompted clarification of public functions versus environment-adapting boundaries and version-sensitive D acceptance. These failures are retained to explain the regression cases.

Limitations: sampled agents used the available local agent harness; no cross-model or statistical reliability claim is made. Acceptance is a manual semantic assessment, not proof that all future maps are correct, that every response meets a mechanical word limit, or that text2agent tests pass. A's passing runs preceded the final call-order clarification; that clarification was exercised by the subsequent C runs. B, D and E were not repeated after that clarification. Re-run this procedure for substantive changes and target models before relying on deployment-specific behavior.

## Hierarchy Revision Observations

Run date: 2026-10-20. Fresh local Kilo CLI sessions answered F independently: five no-skill controls before editing, one completed pre-edit skill baseline, five initial guided samples, and five final guided samples after a wording correction. Each received the same synthetic task, without acceptance criteria. Guided sessions loaded the full skill and contract as system instructions. Application tools were denied; no inspected project code or tests were executed. Responses were manually assessed, not scored by an automated runner. Session answers remain in the local harness, not bundled here.

### Baseline

All five controls retained fourteen peer contracts, sometimes under category headings, rather than a root-only semantic hierarchy with nested full cards. The pre-edit skill also produced fourteen peers: "Each contract has a separate card because the snapshot identifies distinct contracts with separate callers." Distinct contracts need preservation, not necessarily peer placement.

| Session | Outcome / evidence |
| --- | --- |
| `ses_ea62f356affewymLc2NhUmib5E` | Fail F: fourteen peer rows under categories such as "Entry Points" |
| `ses_ea62bea73ffe0RU8fGhtBXmZMC` | Fail F: "All 14 substantive contracts are mapped separately below"; no responsibility-parent cards |
| `ses_ea62be94dffeznaCKIuionJDZq` | Fail F: single fourteen-row "Capability Map" |
| `ses_ea62be949ffe1NCqB2Xw7ljY5z` | Fail F: fourteen peer contracts under category headings |
| `ses_ea62be96fffexaQJ2nP5Ngujqu` | Fail F: numbered peer cards 1 through 14 |
| `ses_ea6259b66ffezmVgLxvNMxd4av` | Pre-edit skill: fail F; fourteen peers in the five-class index and fourteen peer cards |

### Guided Samples

Initial wording bounded all five maps to eight or nine roots and retained all fourteen contracts. Three still overstated patch guarantees in overview rows despite caveats in detailed cards. The contract was tightened so summaries cannot exceed child evidence, then F was repeated in five new sessions.

| Initial guided session | Roots | F outcome |
| --- | --- | --- |
| `ses_ea6216b50ffeMDG073HnBJfeF2` | 9 | Fail: overview says "non-mutating patch contracts" |
| `ses_ea6216b7fffeWSqkF5WBLQtwxB` | 9 | Fail: overview says "non-in-place patching" |
| `ses_ea6216b7affeLzJQ7hKfmKw920` | 9 | Pass: nested contracts and bounded summaries |
| `ses_ea6216b43ffefvxJ9zhvfgrBq4` | 8 | Fail: overview says "immutable graph patching" |
| `ses_ea6216bebffeI3Qqb63qCw1iBb` | 8 | Pass: nested contracts and bounded summaries |

| Final guided session | Roots | F outcome |
| --- | --- | --- |
| `ses_ea61e0b45ffeEq563kj84M0WQD` | 9 | Pass |
| `ses_ea61e0a6cffeJie25xhUbGQBd4` | 8 | Pass |
| `ses_ea61e0984ffety1vmt88mTBoIG` | 9 | Pass |
| `ses_ea61e0995ffeSV5ouhhxWGe4Lr` | 9 | Pass |
| `ses_ea61e0906ffesYgZftGB3zLoI5` | 9 | Pass |

Final samples kept all fourteen contracts with explicit parent placement, full compact cards/evidence, a root-only overview, independent child classifications and scoped relationships. They did not repeat the unsupported mutation guarantee. Group choices and ID spellings varied; this is convergence on the required structure, not an identical canonical decomposition.

### Edge Checks and Limits

| Scenario | Final-wording session | Outcome |
| --- | --- | --- |
| C | `ses_ea61b1e42ffebDHll34QCbLjAc` | Pass: two roots, absent classes explicit, private helper retained as evidence, sequence not mistaken for argument flow |
| G | `ses_ea61b1e83ffek719bfAxwVOKJ8` | Pass: eight roots; all fourteen surviving IDs, evidence and relationships retained; new groups and moves explained |
| H | `ses_ea61b1e27ffehAUer6Jup6GZhV` | Pass: four responsibility roots; twelve run contracts, twelve distinct stores and twelve local protocols retained with nested cards and scoped evidence; common card fields explicitly applied to each instance |

Excluded harness attempts: `ses_ea62a7339ffeQoYIefnJrig3qa` and `ses_ea6291c96ffemRNQUKQ78kBmJG` could not load file attachments under denied tool permissions; their answers explicitly lacked the skill. `ses_ea627d7e4ffe2iWA6yFMcZM7xL` timed out with no completed answer. These are not guided passes or semantic failures. System-instruction loading and a longer blocking timeout allowed subsequent completed runs.

Limits: one local agent/model setup (Kilo 7.6.2, openai/gpt-6-astra), manual semantic assessment, no cross-model or statistical reliability claim. C, G and H each had one final-wording application check, not five controls and five guided repetitions. A, B, D and E were not rerun in this revision; earlier observations above are historical, not validation of the revised wording. No real checkout was inspected for the new scenarios. The standalone package has no configured formatter, linter, type checker, or executable test suite; none was installed. Checks of repository diff/whitespace do not replace these agent behavior tests.
