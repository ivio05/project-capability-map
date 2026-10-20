# Map Contract

## Scope and Granularity

Use this contract for build, reuse, and refresh modes. Output prose in the user's language; keep class identifiers and block IDs stable when updating a saved map.

A block is a coherent responsibility with a describable contract and a recognizable way to use it. A contract may be a function, protocol, configuration/registration convention, or lifecycle, not necessarily a public API. It can span files or share a file with another block. Dependencies are normal: this is a map of the existing project, not a redesign into independent services. Classification does not require restructuring code.

Start from an available map or explicit project classification. The entire requested map has at most ten top-level semantic responsibility blocks, counted across all classes and projects, not ten per class, directory, or service. Ten is a ceiling, not a target; a small library may need only two blocks.

Build the hierarchy by cohesive responsibility or scenario, not folders, arbitrary equal chunks, or a miscellaneous bucket. Retain substantive distinct contracts, or capabilities callers select separately, as nested children with full cards and evidence. Keep minor helpers as implementation evidence. Choose meaningful parents rather than hiding an unchanged flat catalog under a generic project wrapper.

Containment is navigation only: a parent summarizes a responsibility, not necessarily a runtime component, owner, caller, or new facade. State that distinction on grouping cards; their entry/usage fields point to actual child contracts, not invented APIs. Each block has one placement and a stable ID independent of its parent; cross-link shared use instead of duplicating cards. Moving a surviving block preserves its ID.

Overview and parent summaries carry only guarantees supported by child evidence; detailed caveats do not repair an overstated summary. For example, returning a new graph does not establish input immutability or a non-mutating operation.

In a monorepo, identify independently operated project scopes, then group their responsibilities within the same global ceiling. Preserve service identity on nested cards and evidence, even when a cohesive parent spans services. Do not infer shared instances, buses, stores, or interactions from containment or similar names.

## Classification

| Class | Includes | Selection rule |
| --- | --- | --- |
| Boundaries | HTTP/CLI entry points, external-service adapters, import/export interfaces | Main purpose is adapting an external environment, protocol, or exchange format. Record inbound, outbound, or bidirectional direction. |
| Scenarios and orchestration | Agent loops, generation pipelines, job coordination, startup assembly | Main purpose is coordinating a use case or lifecycle. Startup wiring may be part of a scenario rather than a separate block. |
| Internal capabilities | Graph validation, patching, conversion, resolution, calculations | Main purpose is one specific internal operation, even when several callers use it. |
| Cross-cutting mechanisms | Event protocols, authorization enforcement, diagnostic propagation, deadline accounting | Main purpose is a shared behavioral convention spanning distinct responsibilities, not merely utility reuse. |
| State and resources | Persistence, caches, registries, connection/execution pools | Main purpose is storing/accessing state or owning resource lifecycle. |

Classes are semantic roles, not programming-language classes or module/directory structure. One primary class per block; add secondary roles rather than duplicating the same block. Classify a database-backed repository by state ownership and a remote-generation client by boundary translation. Separate a client-facing polling interface from its buffer only when the distinction helps explain contracts. Empty classes are valid; use "absent in inspected scope" or "not investigated", never invent blocks to fill them.

An ordinary public library function is an entry to its capability, not a Boundaries block solely because external code can call it. Classify it by its work. A distinct library facade adapting a protocol or exchange format can qualify as a boundary; API visibility alone cannot.

## Build Output

Return these sections in order. Compact tables and inline cards are acceptable; no required field disappears just to shorten an answer.

### 1. Scope and Provenance

Identify project root/name, inspection date, revision and worktree state if available, requested scope, and what was actually inspected. For supplied excerpts, identify them as excerpts, not a filesystem inspection. State static inspection versus executed checks, access limitations, and explicit exclusions. Unknown revision or runtime behavior stays unknown.

### 2. Classified Index

The overview lists only root blocks, grouped by primary class. Each row gives a semantic name, stable local ID, responsibility, reuse trigger, and status. Keep child detail nested under its parent, not flattened into extra overview peers. Use names such as "Run generation" rather than `runs.py`. Paths belong in evidence, not as the unit of classification.

Show coverage of all five classes across every hierarchy level. A class represented only by children points to their IDs; it is not absent merely because no root has that primary class. Mark genuinely absent/uninspected classes explicitly. Classify each child by its own responsibility, not by inheritance from its parent.

Status describes evidence, not approval: observed connected in this snapshot, documented deprecated, candidate with unknown wiring, or unknown. Absence of visible callers does not prove dead code. A wired example supports "used here", not necessarily "the universal preferred API".

### 3. Relationships and Main Paths

Use block IDs and labeled edges, in a table or text chains. Distinguish calls, data transformations, notification delivery, state read/write, registration, and resource ownership. Cite decisive sources. Describe the major entry-to-result scenarios; keep notification paths separate from commands or mutations.

Use the actual participating child IDs for detailed relationships. Root-level summaries must name the supporting child edges and their scope; a child's call does not establish a parent-wide call, ownership, or every-to-every dependency. Keep navigation containment distinct from runtime edges.

Call order alone does not prove data flow: "calls A, then B" does not establish that B receives A's result. Record the observed sequence and unknown arguments separately. Label a proposed composition as a recommendation supported by contracts, not an existing call-site fact.

For events, trace event type -> producer -> active scope/registration -> consumer -> output/retention. Check synchrony, ordering, error propagation, subscription scope, and durability in code; record unknowns rather than importing broker guarantees.

### 4. Block Cards

Every root and child at every depth has a full card, with child cards nested under their parent. The root ceiling limits overview breadth, not retained contracts or required fields. Merge fields on one line when brevity is needed:

- **ID, name, class, status:** primary role and optional secondary roles; parent ID or root, and project scope where needed.
- **Purpose and fit:** responsibility, when to use, and what is outside its contract.
- **Entry and contract:** actual symbols/protocols, inputs, outputs, errors, and side effects. For state/resources include ownership, lifetime, and transaction/cleanup boundaries when known.
- **Wiring and relationships:** how it becomes active, required context/configuration, collaborators, and consumers. Mark unknown registration explicitly.
- **Existing usage:** a real call site or registration showing how to reuse it; show a short grounded integration recipe. If none is available, say so rather than fabricate one.
- **Constraints:** relevant ordering, failure, consistency, concurrency, and compatibility rules. Separate established behavior from unanswered questions.
- **Evidence:** project-relative paths plus symbols and line ranges where available. Attach evidence to the claims it supports, not just a bibliography.

### 5. Coverage and Gaps

For each coverage axis list the relevant root/child IDs, evidence, and omissions: external input/output, main scenarios, substantive local operations, shared protocols, and state/resource ownership. Audit all levels for unique placement, complete cards, valid cross-links, and preserved distinct contracts as well as the global root count. Include relevant startup, shutdown, failure paths, runtime-selected implementations, and extension areas. Reading tests can corroborate intended behavior but does not demonstrate passing tests.

"Complete for inspected scope" requires every identified major responsibility in that scope to be represented and relationships traced; it is not a claim that every runtime behavior was proven. Uninspected potentially relevant areas make project-wide coverage partial. List unanswered questions, documentation conflicts, competing implementations, and the next bounded inspection needed. Avoid unsupported percentages.

## Task Reuse Output

For a bounded task, refresh affected cards and relationships rather than rebuilding the entire map. Use the skill's full-build conditions when broader investigation is needed. Return:

1. Task and relevant block IDs, including collaborators needed for a safe integration.
2. Current evidence: rechecked implementation, entry/contracts, callers, registration/wiring, representative usage, and applicable constraints; identify stale claims and justify discrepancies with the existing decomposition explicitly.
3. Integration path: which existing mechanism fits, where to connect, and which outputs/state changes remain separate.
4. Fit and gaps: explain why reuse is appropriate or why no suitable path was established. Distinguish confirmed incompatibility from missing evidence.

For a full refresh of a legacy flat map exceeding ten roots, coherently regroup the whole requested scope. Preserve surviving IDs, contracts, evidence, and relationships; explain moves, new grouping IDs, and any evidence-backed removal. Do not silently keep the first ten, drop outlying contracts, or call a local slice a refreshed full map. If investigation is incomplete, retain known coverage and report the remaining regrouping/evidence gaps explicitly.

If the task also authorizes implementation, this map informs that work but does not widen its permission. Otherwise return guidance without changing code. Update saved content only with write permission; preserve confirmed semantic names, boundaries, stable IDs, and unaffected material.

## Worked Example: Events

This is a fictional inspected snapshot, not a universal event architecture.

**EVT / Scoped notifications / Cross-cutting mechanisms / Observed connected.** Use to notify observers of run progress; not to mutate run state or schedule reliable background work. `events.py::publish` invokes listeners synchronously, in order, within an active context-local scope; nested scopes add listeners. Exceptions propagate and stop later delivery; no listeners means discard (`events.py:5-30`).

The run scenario publishes `RunUpdated` after calling `store.save` (`runs.py::start`, `runs.py:20-70`). Startup connects diagnostic output and an in-memory polling consumer (`boot.py:8-28`, `console.py:5-18`, `polling.py:8-38`). To add an observer, follow that existing registration path and account for synchronous failure propagation. The exact registration API must be inspected before proposing executable code. Polling has no persistence or replay; the run store owns persisted state (`polling.py:8-38`, `store.py:10-60`).

Relationships: startup registers EVT consumers; run generation writes the run store; run generation publishes EVT; EVT delivers to diagnostic output and polling. These are different edges, not one "events handle everything" dependency.

A README claiming "reliable async messaging" conflicts with this implementation. Preserve the conflict and use the observed code behavior for this snapshot. The example supplies no transaction atomicity guarantee across saving and notifying.
