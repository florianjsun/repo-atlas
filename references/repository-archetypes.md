# Repository archetypes

Read only the section matching the repository. Combine sections for genuine hybrids and monorepos.

## Application or service

Prioritize:

- user or system actors and externally visible behavior;
- HTTP/RPC routes, middleware, handlers, jobs, consumers, and schedulers;
- domain operations, persistence, caches, queues, and external services;
- authentication, authorization, errors, retries, transactions, and migrations;
- integration tests and deployment units.

Representative flows should begin with requests, events, jobs, or operator actions and end with responses or side effects.

## Library or SDK

Prioritize:

- intended consumers and supported use cases;
- package exports, public types, constructors, builders, and extension hooks;
- lifecycle, configuration, compatibility, and error contracts;
- internal abstraction boundaries versus public API;
- examples, contract tests, fixtures, release/versioning rules, and migration guides.

Representative flows should begin with a documented public API call. Do not describe internal helpers as public extension points.

## CLI or developer tool

Prioritize:

- commands, flags, configuration precedence, environment, and stdin;
- parsing, dispatch, core operations, output formats, exit codes, and errors;
- filesystem/network side effects, idempotency, and platform differences;
- plugin/subcommand mechanisms and shell integration;
- end-to-end command tests and packaging/distribution.

Representative flows should begin with a real command and end with observable output, exit status, or changed resources.

## Framework or plugin platform

Prioritize:

- application authors, plugin authors, and operators as distinct users;
- lifecycle, registration, discovery, hooks, middleware, and dependency injection;
- configuration schema, extension contracts, compatibility, and ordering;
- generated code, reflection, dynamic loading, and runtime-only relationships;
- examples and contract/compatibility tests.

Clearly separate statically proven relationships from framework runtime behavior.

## Infrastructure, controller, or operator

Prioritize:

- desired-state resources and reconciliation loops;
- control plane versus data plane;
- resource models, state transitions, provisioning, rollout, and cleanup;
- failure recovery, retries, backoff, eventual consistency, and observability;
- permissions, secrets, external systems, deployment manifests, and integration tests.

Representative flows should follow a resource or event through reconciliation to observable state.

## Monorepo

First identify:

- workspace/package boundaries and ownership;
- deployable units, shared libraries, tools, and generated packages;
- dependency direction and cross-workspace contracts;
- root versus package-specific commands, configuration, CI, and releases;
- which subgraph is relevant to the user request.

Generate a thin root Wiki and deeper pages/cards only for high-value workspaces. Do not flatten every package into one architecture story. For a task, restrict the initial scope to the owning workspace and expand only along evidenced dependencies.

## Documentation, specification, or examples repository

Prioritize:

- information architecture, source formats, generators, validation, and publishing;
- normative versus explanatory content;
- examples that act as executable specifications;
- versioning, localization, links, and contribution workflow.

Do not invent a runtime architecture when the repository's primary product is documentation or specification.
