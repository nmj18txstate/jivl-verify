# JIVL RFC Process

JIVL RFCs are **internal project design documents**. They are not IETF
RFCs, JEPs, JSRs, or official standards of any kind, and use of the term
"RFC" here carries no affiliation with those processes.

## When an RFC Is Required

Per `AGENTS.md` and `GOVERNANCE.md`, a change requires an RFC before
implementation if it affects: verification policy, finding severities,
badge semantics, module boundaries, CLI flags, or report/badge/manifest
schemas. Bug fixes, test additions, and documentation fixes do not need
one.

## Lifecycle

`Draft` -> `Proposed` -> `Accepted` -> `Implemented` | `Superseded` | `Rejected`

- **Draft** — under development, not yet ready for public review.
- **Proposed** — ready for public review.
- **Accepted** — explicitly approved by Nityam Jigyasu (see `GOVERNANCE.md`)
  or a future authorized maintainer; implementation may proceed.
- **Implemented** — the corresponding implementation and tests exist.
- **Superseded** — a later RFC replaces this one; link both directions.
- **Rejected** — intentionally not proceeding; the "Decision History"
  section records why.

An RFC must not set its own status to `Accepted` or `Implemented`; only the
Lead Maintainer (or a future authorized maintainer) may make that change.

## Required Sections

Every RFC must contain: Title, Author, Status, Created date, Last updated
date, Summary, Motivation, Goals, Non-goals, Design, Security
considerations, Compatibility considerations, Alternatives, Open
questions, Decision history.

## Current RFC Index

| RFC | Title | Status |
|---|---|---|
| [RFC-0001](RFC-0001-jivl-vision.md) | JIVL Vision | Draft |
| [RFC-0002](RFC-0002-verification-report-model.md) | Verification Report Model | Draft |
| [RFC-0003](RFC-0003-jvm-compatibility-metadata.md) | JVM Compatibility Metadata | Draft |
| [RFC-0004](RFC-0004-security-model.md) | Security Model | Draft |
| [RFC-0005](RFC-0005-provenance-policy.md) | Provenance Policy | Draft |
| [RFC-0006](RFC-0006-effectiveness-evaluation.md) | Effectiveness Evaluation | Draft |
| [RFC-0007](RFC-0007-jvmskills-integration.md) | JVMskills.com Integration | Draft |
