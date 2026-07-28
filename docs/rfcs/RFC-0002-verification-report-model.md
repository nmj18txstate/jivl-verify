# RFC-0002: Verification Report Model

- **Author:** Nityam Jigyasu (@nmj18txstate)
- **Status:** Draft
- **Created:** 2026-07-27
- **Last updated:** 2026-07-27

## Summary

Define the report model: findings (not a single score), statuses, severities,
categories, and the canonical serialization/digest approach across text,
Markdown, and JSON formats.

## Motivation

Reports are the artifact everything else (badges, registry, CI job
summaries, the JVMskills.com proposal) is built on. Getting the shape right
once, with schema validation from the start, avoids incompatible ad hoc
formats appearing per consumer later.

## Goals

- One finding model shared by every rule-producing module.
- A stable `schemaVersion` from the first release.
- Deterministic, canonically-ordered JSON so `reportDigest` is meaningful.
- Clear separation of naturally-variable fields (timestamps, environment
  summary) from canonical, digest-included content.

## Non-Goals

- Not defining a single aggregate score (see ADR-0003 — that is settled
  separately and this RFC does not reopen it).
- Not defining the badge model in detail (see RFC-0005... — actually see
  the dedicated `docs/badge-and-attestation-model.md` and ADR-0005; this
  RFC covers the report, badges consume it).

## Design

- `Finding`: `ruleId`, `category`, `severity`, `status`, `title`,
  `explanation`, `evidence`, `file` (optional), `line` (optional),
  `remediation`, `verifierVersion`.
- Statuses: `PASS`, `WARN`, `FAIL`, `SKIPPED`, `ERROR`.
- Severities: `INFO`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`.
- Categories: `STRUCTURE`, `CONTENT`, `REFERENCE`, `SECURITY`,
  `PROVENANCE`, `LICENSE`, `JAVA`, `MAVEN`, `GRADLE`,
  `JDK_COMPATIBILITY`, `FRAMEWORK_COMPATIBILITY`, `BUILD`, `EVALUATION`,
  `REPORTING`.
- JSON report top-level fields: `schemaVersion`, `jivlVersion`, `reportId`,
  `reportDigest`, `startTime`, `completionTime`, target skill identity,
  optional `commitSha`, environment summary, `findings[]`, Java evidence,
  Maven evidence, Gradle evidence, compatibility evidence, evaluation
  evidence, aggregate counts, `attestationType`.
- Digest: SHA-256 over canonical content, explicitly excluding
  `startTime`/`completionTime`/environment fields that vary run-to-run
  without the underlying skill changing; the exact excluded-field list
  must be documented alongside the digest computation, not left implicit.
- `Clock` injection everywhere a timestamp is produced, for deterministic
  tests.

## Security Considerations

Reports must never contain unredacted secret evidence (ties to
`docs/threat-model.md`'s "Secrets leak through JIVL's own output" threat).

## Compatibility Considerations

`schemaVersion` must be bumped, per `GOVERNANCE.md`'s "Rules for Changing
Verification Policy," whenever a breaking report-shape change occurs, with
an accompanying `schemas/jivl-report.schema.json` update.

## Alternatives

- **Free-form/unversioned JSON.** Rejected: would make the registry's
  "validate before render" defense (ADR-0002/ADR-0006) meaningless.

## Open Questions

- Exact list of "environment summary" fields to include without leaking
  local absolute paths (must satisfy the registry's "never expose local
  paths" requirement even when reports are generated locally).

## Decision History

- 2026-07-27: Accepted; implementation deferred to Phase 1 (see
  `docs/implementation-status.md`).
