# Skill Acceptance Scenarios

These are manual, fresh-context agent tests, not application tests. No external dependency or test runner is required. Never mutate the projects used for inspection or execute their application/test code. Repository instructions still apply. This file is for skill maintainers, not required reading during normal skill use.

## Procedure

1. Give a fresh agent the scenario prompt without the skill as a control. Capture its actual answer, not a self-evaluation.
2. Give another fresh agent the identical prompt plus `SKILL.md` and `references/map-contract.md`. Do not supply the acceptance criteria as hints.
3. Manually score the answer against every criterion. Record omissions and unsupported claims with quotations. A scenario passes only if all its criteria hold; brevity may compress required fields, not remove them.
4. For the fixed-snapshot wording test, run at least five independent controls and five guided samples. Other scenarios require separate fresh-context application checks. Re-run affected cases after changing instructions.

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
