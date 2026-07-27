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

`Draft` -> `Proposed` -> `Accepted` | `Rejected` -> (optionally) `Superseded`

- **Draft** — being written, not yet ready for review.
- **Proposed** — open for discussion.
- **Accepted** — the Lead Maintainer (see `GOVERNANCE.md`) has accepted it;
  implementation may proceed.
- **Rejected** — will not be implemented as proposed; the "Decision
  History" section records why.
- **Superseded** — a later RFC replaces this one; link both directions.

## Required Sections

Every RFC must contain: Title, Author, Status, Created date, Last updated
date, Summary, Motivation, Goals, Non-goals, Design, Security
considerations, Compatibility considerations, Alternatives, Open
questions, Decision history.

## Current RFC Index

| RFC | Title | Status |
|---|---|---|
| [RFC-0001](RFC-0001-jivl-vision.md) | JIVL Vision | Accepted |
| [RFC-0002](RFC-0002-verification-report-model.md) | Verification Report Model | Accepted |
| [RFC-0003](RFC-0003-jvm-compatibility-metadata.md) | JVM Compatibility Metadata | Accepted |
| [RFC-0004](RFC-0004-security-model.md) | Security Model | Accepted |
| [RFC-0005](RFC-0005-provenance-policy.md) | Provenance Policy | Accepted |
| [RFC-0006](RFC-0006-effectiveness-evaluation.md) | Effectiveness Evaluation | Accepted |
| [RFC-0007](RFC-0007-jvmskills-integration.md) | JVMskills.com Integration | Accepted (proposal stage; awaiting external feedback) |
