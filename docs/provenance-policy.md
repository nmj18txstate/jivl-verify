# Provenance Policy

## Purpose

`jivl.yaml` lets a skill author declare origin, authorship, licensing, and
tested compatibility. JIVL validates the manifest's *shape and internal
consistency*. It cannot and does not independently prove the declarer is
telling the truth — see "Attestations, Not Proof" below.

## Schema (schema-version 1)

```yaml
schema-version: 1

skill:
  version: "1.0.0"

provenance:
  origin: original            # original | licensed | public-domain | mixed
  author: "Example Author"
  source-repository: "https://example.invalid/repository"
  third-party-material: false
  employer-authorization-required: false
  source-notes:
    - "Original examples created for this skill"

license:
  identifier: "Apache-2.0"
  file: "LICENSE"

compatibility:
  java:
    minimum: 17
    maximum: 25
    tested:
      - 17
      - 21
  frameworks:
    spring-boot:
      declared:
        - "3.5"
        - "4.1"
      tested:
        - "4.1.0"

verification:
  example-projects:
    - "examples/demo-app"
  evaluation-specifications:
    - "evaluations/basic.yml"
```

Full field validation rules live in `schemas/jivl-manifest.schema.json` and
are enforced in `jivl-core`/`jivl-security` manifest-parsing code.

## Validation Rules (summary)

- `schema-version` must be present and a supported value.
- `skill.version` must be a non-empty valid version string.
- `provenance.origin` must be one of `original`, `licensed`,
  `public-domain`, `mixed`.
- `provenance.author` must be present.
- `provenance.source-repository`, if present, must be a syntactically valid
  URI.
- `provenance.third-party-material` and `provenance.employer-authorization-required`
  must be explicit booleans — there is no implicit default that assumes
  the safer answer for the declarer.
- `license.identifier` must be present; `license.file`, if present, must
  resolve to a file that actually exists inside the skill directory.
- `compatibility.java.minimum` <= `maximum`, and every value in `tested`
  must fall within that range.
- Framework version declarations must be syntactically valid; `tested`
  framework versions should be a subset of `declared` versions.
- Every path referenced anywhere in the manifest (`license.file`,
  `verification.example-projects`, `verification.evaluation-specifications`)
  must resolve *inside* the skill directory — path traversal and symlink
  escape are rejected the same way they are for `SKILL.md` references (see
  `docs/threat-model.md`).

## Missing Manifest

A missing `jivl.yaml` produces **WARN**, never FAIL — provenance
declaration is encouraged, not mandatory, in this MVP.

## Attestations, Not Proof

Every place JIVL surfaces provenance information — in the CLI, in reports,
and in the registry UI — must make clear these are **attestations by the
declaring author**, not facts independently verified by JIVL. JIVL checks
internal consistency (e.g., "does the manifest contradict itself or the
POM/build file") and structural validity, not truthfulness of the
underlying claim (e.g., it cannot confirm `provenance.author` is the real
author, or that `third-party-material: false` is actually true).

## Relationship to Licensing Checks

`license.identifier` in `jivl.yaml` is one signal. JIVL does not perform
deep, general-purpose software license compliance scanning (e.g., scanning
every dependency's transitive license) in this MVP — that is future scope,
not implemented here. Findings in the `LICENSE` category in this MVP are
limited to manifest-declared license shape and the existence of the
referenced license file.
