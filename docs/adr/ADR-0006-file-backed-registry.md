# ADR-0006: File-Backed Registry, No Database, in This MVP

- **Status:** Accepted
- **Date:** 2026-07-27

## Context

`jivl-registry` needs to list, search, and filter previously generated
reports. A database is the conventional choice, but introduces migration
tooling, connection configuration, and an additional moving part this MVP
does not yet need, given reports are just JSON files already produced by
the CLI/CI.

## Decision

`jivl-registry` reads JSON reports directly from a configured directory
(`jivl.registry.data-directory`, defaulting to `registry-data/`) through a
`VerificationReportRepository` abstraction, implemented by
`FileSystemVerificationReportRepository`. Search/filter/pagination are
performed in-process over the loaded report set. No database is introduced
in this MVP.

## Alternatives Considered

- **Embedded database (H2/SQLite) for search/pagination.** Rejected for
  now: adds schema/migration surface area disproportionate to the MVP's
  scale, and the repository abstraction already isolates this decision so
  it can be swapped later without touching controllers or DTOs.

## Consequences

- Startup cost scales with the number of report files present; acceptable
  for an MVP-scale registry, and revisitable (behind the same
  `VerificationReportRepository` interface) if report volume grows enough
  to matter.
- Malformed report files must be handled defensively (logged and skipped,
  never crash the registry) since there is no schema-enforcing database
  layer sitting in front of them — schema validation happens in
  application code against `schemas/jivl-report.schema.json` instead.
