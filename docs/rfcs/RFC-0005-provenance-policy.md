# RFC-0005: Provenance Policy

- **Author:** Nityam Jigyasu (@nmj18txstate)
- **Status:** Draft
- **Created:** 2026-07-27
- **Last updated:** 2026-07-27

## Summary

Define the optional `jivl.yaml` provenance manifest: schema, validation
rules, and the explicit "attestation, not proof" framing.

## Motivation

Skill provenance (origin, authorship, licensing, tested compatibility)
matters for adoption decisions, but JIVL cannot independently verify
authorship or licensing truthfulness — only internal consistency and
structural validity. Overstating what manifest validation proves would be
dishonest.

## Goals

- A clear, versioned (`schema-version`) manifest schema.
- Validate internal consistency: Java/framework version ranges, tested
  values falling within declared ranges, referenced files existing and
  staying inside the skill directory (no traversal, no symlink escape).
- Missing manifest is `WARN`, not `FAIL` — encouraged, not mandatory.

## Non-Goals

- Not proving authorship or license truthfulness.
- Not a general dependency-license compliance scanner.

## Design

Full schema and validation rule list: `docs/provenance-policy.md`. Schema
file: `schemas/jivl-manifest.schema.json` (Phase 1 deliverable).

## Security Considerations

Every path field in the manifest goes through the same safe-path/symlink-
escape checks as `SKILL.md` references (`docs/threat-model.md`).

## Compatibility Considerations

`schema-version: 1` is the only supported version at MVP launch; a future
`schema-version: 2` would need explicit migration/compatibility notes
here, updated in place, not a silent behavior change.

## Alternatives

- **Mandatory manifest.** Rejected: would raise the barrier to entry for
  simple skills; `WARN`-not-`FAIL` was chosen to encourage without
  blocking.
- **Free-form provenance text field instead of structured schema.**
  Rejected: structured fields are what make automated consistency checks
  (e.g., tested-within-declared-range) possible at all.

## Open Questions

- Whether `provenance.source-notes` should eventually support structured
  per-note attribution rather than a plain string list; deferred, no
  strong need identified yet.

## Decision History

- 2026-07-27: Accepted; implementation deferred to Phase 1.
