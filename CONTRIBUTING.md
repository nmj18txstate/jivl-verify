# Contributing to JIVL

Thank you for considering a contribution. JIVL is an early-stage,
single-maintainer project (see `GOVERNANCE.md`), so please read this before
opening a large pull request.

## Before You Start

- For anything beyond a small fix, open an issue first describing the
  problem or proposal.
- Changes to verification policy, finding severities, badge semantics,
  module boundaries, CLI flags, or report/badge schemas require an RFC
  (`docs/rfcs/README.md`) before implementation work begins.
- Read `AGENTS.md` for the module map, dependency direction rules, and
  required local verification commands.

## Development Setup

Requirements: JDK 21, and either the bundled Maven Wrapper (preferred) or a
locally installed Maven that satisfies the version in `AGENTS.md`.

```bash
./mvnw -B -ntp clean verify
```

On Windows:

```bat
mvnw.cmd -B -ntp clean verify
```

## Coding Standards

See `AGENTS.md` "Java style rules" for the authoritative list. In summary:
constructor injection, immutable records for value objects, no wildcard
imports, no field injection, no swallowed exceptions, explicit UTF-8, and no
Lombok or preview language features in the Java 21 baseline.

## Tests

- New rules (security, structural, compatibility) need both a positive and
  a negative fixture test.
- Filesystem tests must use `@TempDir`; no test may require network access
  or an installed Maven/Gradle executable — use fake/injectable process
  abstractions.
- Run the full reactor (`./mvnw -B -ntp clean verify`) before opening a PR.

## Sample Skills

New or modified sample skills under `samples/` must be original content,
licensed Apache-2.0, and must not be derived from commercial books, courses,
or paid material (see `AGENTS.md` "Security constraints").

## Pull Requests

Use the template in `.github/pull_request_template.md`. Describe: what
changed, why, which module(s) are affected, what tests were added, and
whether an RFC governs the change (link it).

## Security Issues

Do not open a public issue for a suspected vulnerability — see
`SECURITY.md`.

## Attribution

By contributing, you agree your contribution is licensed under Apache-2.0,
matching the rest of the repository (see `LICENSE` and `NOTICE`).
