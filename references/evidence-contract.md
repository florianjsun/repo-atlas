# Evidence contract

Use this contract for every Repo Atlas operation. Prefer honest gaps over smooth but unsupported explanations.

## Claim classes

| Class | Meaning | Minimum support | Presentation |
|---|---|---|---|
| Static fact | Directly visible in source, config, schema, or a deterministic index | One exact source range | State as fact within the analyzed snapshot |
| Documented claim | README, ADR, Issue, PR, release, or comment says something | One immutable or version-matched document source | Say that the source documents the claim |
| Runtime observation | Observed from an executed test, trace, log, or service | Command and captured result | State what was observed and under which conditions |
| Synthesis | Several supported facts imply a useful higher-level conclusion | Two or more supporting facts, or one explicit authoritative source | Show the reasoning briefly |
| Inference | Plausible interpretation not established by repository evidence | At least one related source | Label clearly; never draw as a certain edge |
| Unknown | Evidence was not found or cannot establish the answer | Search scope or query | State what is unknown and how to verify it |
| Conflict | Sources disagree or refer to different versions | Both conflicting sources | Show both; do not silently choose one |

## Evidence priority

Choose evidence appropriate to the claim. A useful default order is:

```text
runtime observation
> executable tests and schemas
> implementation and configuration
> version-matched ADRs and official docs
> Issues, PRs, and releases
> comments and naming
> inference
```

This is not a universal truth hierarchy. For design intent, a version-matched ADR may be stronger than implementation. For current behavior, source and tests usually outrank old prose.

## Citation format

For conversation output, use clickable local file links with a single starting line when supported by the host. For generated artifacts, use repository-relative links and include a compact evidence block:

```yaml
claim_type: static_fact
snapshot: <commit-or-working-tree>
evidence:
  - path: src/example.ts
    symbol: Example.run
    lines: 24-61
```

Use stable symbols in addition to line numbers. Line numbers help immediate inspection; symbols and snapshot metadata help after code movement.

## Rules for trustworthy explanations

- Cite every important architecture boundary, entry point, state mutation, external side effect, and test relationship.
- Do not cite a whole directory when a narrower file or symbol supports the claim.
- Do not treat the presence of a citation as semantic support; reopen and check the cited text.
- Treat static calls as possible paths, especially with dependency injection, reflection, decorators, generated code, plugin loading, callbacks, queues, or RPC.
- Do not infer product intent from names alone. Without an ADR, Issue, PR, or explicit document, call the reason unknown.
- Report documentation/code disagreements and version mismatches.
- When saying “not found,” name the inspected scope or search terms.
- Avoid numeric confidence scores. Explain evidence strength and missing information directly.
- Preserve source terminology and identifiers even when explaining them in another language.

## Diagram rules

- Use solid edges only for directly supported relationships.
- Use dashed edges for inference or likely runtime relationships.
- Add evidence below the diagram for important edges.
- Do not imply exhaustive coverage from a partial diagram.
- Quote labels containing punctuation and escape repository-controlled text.
- Do not use Mermaid `click`, HTML, scripts, or external resources.

## Final evidence QA

- [ ] The snapshot and dirty status are recorded.
- [ ] Major claims have narrow, semantically supporting evidence.
- [ ] Static and runtime relationships are distinguished.
- [ ] Documentation claims are not presented as observed behavior.
- [ ] Inferences, conflicts, and unknowns are visible.
- [ ] Diagram edges follow the evidence rules.
- [ ] Cited files, symbols, and lines still exist.
- [ ] Excluded scope and validation commands are recorded.
