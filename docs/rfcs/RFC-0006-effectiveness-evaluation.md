# RFC-0006: Effectiveness Evaluation

- **Author:** Nityam Jigyasu (@nmj18txstate)
- **Status:** Draft
- **Created:** 2026-07-27
- **Last updated:** 2026-07-27

## Summary

Define the provider-neutral agent-effectiveness evaluation model:
baseline-vs-with-skill task execution, acceptance criteria, and the strict
rule against fabricating results.

## Motivation

"This skill improves your agent" is a claim that needs an actual
before/after comparison to back it up. Content-quality scoring (as some
competitors offer — see `docs/competitive-landscape.md`, item 8) is a
different, weaker claim than a measured effectiveness improvement. JIVL
keeps these clearly separate.

## Goals

- Parse and schema-validate an evaluation specification (tasks, fixture
  projects, acceptance criteria, optional baseline).
- Model baseline results, with-skill results, and acceptance-criteria
  outcomes without ever inventing values for a criterion that did not
  actually run.
- Report exactly one of: evaluation-spec valid, deterministic fixture
  checks completed, agent baseline execution completed, agent with-skill
  execution completed, effectiveness comparison available, effectiveness
  comparison unavailable, evaluation pending — and default to "pending"
  whenever no agent provider adapter is configured.
- Define provider-neutral adapter interfaces (Codex, Claude Code, GitHub
  Copilot, JetBrains-based agents, local agent runners) without
  implementing any actual provider authentication or remote calls in this
  MVP.

## Non-Goals

- Not implementing any remote AI-provider integration, authentication, or
  secret handling in this MVP.
- Not claiming improvement from a single run or from content-quality
  signal alone.

## Design

Full specification format and lifecycle: main project brief's "Agent
Effectiveness Evaluation" section, implemented in `jivl-evaluations`.
Schema file: `schemas/jivl-evaluation.schema.json` (Phase 1 deliverable).
Path traversal in spec references (`skill-path`, `fixture-project`,
`prompt-file`, etc.) is rejected using the same safe-path discipline as
elsewhere (`docs/threat-model.md`).

## Security Considerations

Evaluation fixture projects are still untrusted content; any
build/compile step they require goes through the same `jivl-maven`/
`jivl-gradle` execution boundaries as ordinary verification (ADR-0004),
not a separate, less-guarded path.

## Compatibility Considerations

`schema-version: 1` for the evaluation spec; adding a new acceptance-
criteria `type` is additive, but changing the meaning of an existing type
is a breaking verification-policy change requiring its own RFC.

## Alternatives

- **LLM-as-judge scoring as a stand-in for effectiveness.** Rejected as
  the primary signal: it evaluates content quality, not measured behavior
  change; JIVL treats these as different claims and does not conflate
  them (see RFC-0004's "Alternatives").
- **Ship a built-in provider integration immediately.** Rejected for this
  MVP given the explicit constraint against remote provider
  authentication/secrets; adapters are designed but not implemented.

## Open Questions

- Which acceptance-criteria `type` values beyond `build`/`test` are worth
  standardizing versus leaving as free-form fixture checks; to be
  determined from real Phase 1 usage.

## Decision History

- 2026-07-27: Accepted; implementation deferred to Phase 1.
