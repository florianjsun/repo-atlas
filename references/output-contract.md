# Persistent output contract

Read this file completely before Generate, Update, Sync, or persistent Task output.

## Contents

1. Output tree
2. Planning file
3. Human Wiki
4. Agent Cards
5. Manifest
6. Updating and manual edits
7. Validation

## Output tree

Write only the artifacts justified by the repository and request:

```text
.repo-atlas/
├── plan.yaml
├── manifest.json
├── wiki/
│   ├── README.md
│   ├── architecture.md
│   ├── modules.md
│   ├── runtime-flows.md
│   ├── development.md
│   ├── change-map.md
│   ├── evidence-and-unknowns.md
│   └── tasks/
│       └── <task-slug>.md
└── cards/
    ├── project.yaml
    ├── architecture.yaml
    ├── development.yaml
    └── modules/
        └── <module-slug>.yaml
```

For a small repository, keep only `wiki/README.md`, `evidence-and-unknowns.md`, and the necessary cards. For a monorepo, group Wiki pages and module cards by workspace or deployable unit.

## Planning file

Create `plan.yaml` before broad generation. It is both the generation plan and the user's editable scope control.

```yaml
format_version: 1
repository_type: application
snapshot:
  commit: <sha-or-null>
  branch: <branch-or-null>
  dirty: false
scope:
  include:
    - src/**
  exclude:
    - vendor/**
focus:
  - architecture
  - development
pages:
  - path: wiki/README.md
    purpose: Project orientation and reading path
  - path: wiki/architecture.md
    purpose: Boundaries, components, and dependencies
languages:
  prose: en
  preserve_source_identifiers: true
```

Honor deliberate user edits to include/exclude, focus, pages, and prose language on later runs. Do not expand scope silently.

## Human Wiki

Start every page with compact metadata:

```yaml
---
repo_atlas_format: 1
snapshot_commit: <sha-or-working-tree>
branch: <branch-or-null>
dirty: false
generated_at: <ISO-8601 timestamp>
scope: <short description>
---
```

### `wiki/README.md`

Answer these questions first:

- What does the project do, and who uses it?
- What are the primary interfaces and technology choices?
- What are the major components or packages?
- What is the shortest useful reading path?
- How should a developer run, test, and change it?

Link to deeper pages and list the analyzed snapshot and important limitations.

### `wiki/architecture.md`

Describe system boundaries, component responsibilities, ownership of data/state, dependency direction, external integrations, and one high-level Mermaid diagram. Avoid generic architecture vocabulary that is not evidenced by the repository.

### `wiki/modules.md`

For each important module, record its responsibility, public entry points, direct dependencies, state or data owned, related tests, and likely change triggers. Group low-value leaf folders instead of inventorying every directory.

### `wiki/runtime-flows.md`

Trace one to three representative requests, commands, events, jobs, plugin lifecycles, or library calls. Include entry, guards, core operation, state changes, side effects, errors, output, tests, and known dynamic gaps.

### `wiki/development.md`

Cover prerequisites, setup, build, run, test, lint, formatting, CI, configuration, migrations, and contribution flow. Mark every command as one of:

- `documented`: found in project documentation or scripts but not run now;
- `verified`: executed successfully in the current environment;
- `failed`: executed and failed, with the relevant reason;
- `inferred`: constructed from configuration and not yet validated.

Never describe a documented command as verified unless it was executed.

### `wiki/change-map.md`

Create practical “to change X, start with Y” mappings. Include extension points, reverse dependencies, schemas/configuration, tests, compatibility concerns, and high-risk areas. Describe impact as candidate scope rather than complete scope.

### `wiki/evidence-and-unknowns.md`

List important inferences, conflicts, excluded paths, uninspected subsystems, missing runtime validation, dynamic mechanisms, outdated docs, and recommended verification actions.

### `wiki/tasks/<task-slug>.md`

Use this shape:

1. Task and snapshot
2. Current observable behavior
3. Desired behavior and explicit non-goals
4. Relevant rules, state, and edge cases
5. Entry point and possible execution path
6. Candidate files, symbols, data, and external effects
7. Existing tests and proposed test cases
8. Risks, unknowns, and next verification actions

## Agent Cards

Create short YAML cards optimized for later task context. Keep one responsibility per card and prefer durable symbols over prose-heavy explanations.

```yaml
format_version: 1
id: module.auth
kind: module
title: Authentication module
summary: Validates bearer credentials and attaches the authenticated principal.
snapshot: <commit-or-working-tree>
scope:
  - src/auth/**
interfaces:
  - symbol: authenticateRequest
    path: src/auth/middleware.ts
relations:
  - type: guards
    target: flow.http-request
claims:
  - statement: The middleware rejects requests without a valid bearer token.
    claim_type: static_fact
    evidence:
      - path: src/auth/middleware.ts
        symbol: authenticateRequest
        lines: 18-49
unknowns:
  - Runtime identity-provider behavior was not exercised.
```

Cards may represent the project, a module, a public interface, a representative flow, a development convention, or a high-value decision. Do not create a card for every file or function.

## Manifest

Use `manifest.json` to support refresh and provenance:

```json
{
  "format_version": 1,
  "repository": {
    "commit": "<sha-or-null>",
    "branch": "<branch-or-null>",
    "dirty": false
  },
  "generated_at": "<ISO-8601 timestamp>",
  "scope": {
    "include": ["src/**"],
    "exclude": ["vendor/**"]
  },
  "artifacts": [
    {
      "path": "wiki/architecture.md",
      "sources": ["src/server.ts", "src/routes/index.ts"],
      "content_hash": "sha256:<hash>"
    }
  ]
}
```

Record each generated artifact, its most relevant source files, and a content hash. Exact hashing tools may vary, but use one deterministic method consistently within a repository.

## Updating and manual edits

For Update:

1. Read `plan.yaml` and `manifest.json`.
2. Compare the recorded commit with the requested/current snapshot using Git diff.
3. Map changed source files to artifacts through manifest sources, imports/references, and repository structure.
4. Reinspect only impacted components plus their direct dependents and tests.
5. Update affected Wiki pages and cards, then refresh hashes and snapshot metadata.
6. Validate cross-page links and any overview page that summarizes changed artifacts.

Preserve blocks between these markers exactly unless the user explicitly asks to rewrite them:

```markdown
<!-- repo-atlas:manual:start -->
Human-maintained knowledge.
<!-- repo-atlas:manual:end -->
```

If an artifact's current hash differs from the manifest and the difference is outside a manual block, treat it as a conflict. Merge narrowly when intent is clear; otherwise leave the file untouched, report the conflict, and propose a patch. Never overwrite manual changes silently.

For Sync, treat user-supplied documents and corrections as documented claims. Record their origin and version; do not let them silently override contradictory source or runtime evidence.

## Validation

Validate at least:

- all planned artifacts exist or are explicitly skipped;
- internal Markdown links resolve;
- page and card snapshots agree with the manifest;
- source paths and cited symbols still exist;
- major diagram edges have evidence or inference styling;
- manual blocks are balanced and preserved;
- cards are concise and non-duplicative;
- unverified behavior and excluded scope remain visible.
