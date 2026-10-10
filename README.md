# Project Capability Map

An agent skill for understanding what a codebase already provides before building a parallel solution.

Instead of summarizing directories, the agent maps large semantic building blocks: their responsibilities, contracts, existing usage, constraints, and interactions. The result helps answer: **Which existing part of this project should I use for this task, and how?**

## Five Classes

| Class | Role | Examples |
| --- | --- | --- |
| Boundaries | Adapt external environments, protocols, or exchange formats | HTTP/CLI adapters, model-provider clients, import/export interfaces |
| Scenarios and orchestration | Coordinate an end-to-end task or lifecycle | Agent loops, generation pipelines, startup assembly |
| Internal capabilities | Perform a specific internal operation | Graph validation, patching, conversion |
| Cross-cutting mechanisms | Establish shared behavior across distinct responsibilities | Events, diagnostic propagation, deadline accounting |
| State and resources | Store data or own resource lifecycles | History stores, registries, caches, connection pools |

Blocks can span several files or share one file. Each has a primary class and optional secondary roles. Dependencies are described rather than hidden; the skill does not redesign the project into independent services. Empty or uninspected classes remain explicit.

## Usage

This is an instruction-based skill, not a standalone program or background service. An agent needs access to the project and must load [SKILL.md](SKILL.md) and its linked [map contract](references/map-contract.md). If the agent environment supports skill registration, use its supported installation mechanism. Publishing this repository does not automatically install or activate the skill.

After the skill is available, requests can look like these:

### Build a Map

```text
Use project-capability-map to inspect the project at /path/to/project.
Return a semantic capability map in English, not a file inventory.
Do not modify project files. Mark uninspected areas and unknown behavior.
```

The agent inspects instructions, entry points, startup wiring, call sites, consumers, and state access. It returns scope and provenance, a five-class index, labeled relationships, block cards, and coverage/gaps. Cards include actual entry points, existing usage, restrictions, and source references.

### Save a Map

```text
Use project-capability-map for /path/to/project.
Save the map to docs/project-capability-map.md inside that project.
Do not change application code or overwrite unrelated content.
```

**By default, the map is returned in the conversation.** There is no fixed storage location. File persistence requires an agreed destination and write permission; the path above is an example, not a default. Existing project instructions still apply. Output follows the user's language.

### Reuse or Refresh a Map

```text
Use project-capability-map and docs/project-capability-map.md to explain
how to add client progress reporting using this project's existing mechanisms.
Verify current entry points, wiring, and constraints before recommending reuse.
Do not implement the feature or write files.
```

The agent selects relevant blocks, checks their current code, and reports an integration path, restrictions, stale claims, and genuine gaps. It does not need to rebuild the entire map. Updating a saved map still requires write permission.

## Limits

- A map is navigation, not authority. Current implementation and callers must be checked before reuse; documentation conflicts and uncertainty remain visible.
- Partial inspection produces a partial map. The skill does not guarantee complete runtime coverage or correct answers from every model.
- There is no automatic cross-session memory, map discovery, or background update. A later agent must be directed to the saved map, or discover it through separately configured project instructions.
- Mapping does not authorize refactoring, dependency installation, application execution, or running project tests. Reading test assertions is not evidence that tests passed.
- Existing code is reused only when it fits. Events are not assumed to mutate state, run asynchronously, or provide durable delivery.

## Repository Contents

- [SKILL.md](SKILL.md): agent instructions for building, using, and refreshing maps.
- [references/map-contract.md](references/map-contract.md): classification rules, output contracts, evidence requirements, and a fictional event example.
- [tests/scenarios.md](tests/scenarios.md): fresh-context agent acceptance scenarios, manual observations, and evaluation limitations. These are not an automated test suite; original agent transcripts are not bundled.

The skill has no bundled executable code or dependency requirements. Its evaluation includes synthetic cases and read-only application to a real project; it does not establish passing tests for that project.
