# ADR-0001: Modular Multi-Module Architecture

- **Status:** Accepted
- **Date:** 2026-07-27

## Context

JIVL needs a domain model (findings, statuses, skill identity) usable from
at least three very different front ends: a CLI, a Spring Boot registry
website, and eventually a GitHub Action. It also needs to run untrusted
Maven/Gradle builds in a narrow, auditable place, and must guarantee the
read-only registry can never accidentally gain the ability to execute a
skill or a build.

## Decision

Split the project into a Maven multi-module reactor with one-way
dependency flow: `jivl-core` (no framework dependencies) at the base;
`jivl-security`, `jivl-maven`, `jivl-gradle`, `jivl-reporting` depending
only on `jivl-core`; `jivl-evaluations` depending on `jivl-core` and,
where justified, `jivl-reporting`; `jivl-cli` composing all of the above;
`jivl-registry` depending only on `jivl-core` and `jivl-reporting` and
explicitly forbidden from depending on `jivl-cli`. Enforce this with
ArchUnit tests, not just documentation.

## Alternatives Considered

- **Single monolithic module.** Simpler to start, but nothing would stop
  `jivl-registry` from acquiring a transitive dependency on Maven/Gradle
  execution code over time, silently widening its attack surface.
- **Hexagonal/ports-and-adapters with a separate "application" module per
  front end.** Considered, but for this MVP's scope the simpler
  core-plus-satellites shape was judged sufficient; this can be revisited
  via a future ADR if the CLI and registry composition logic grows enough
  to warrant a shared "application" layer.

## Consequences

- Contributors must think about which module a change belongs in; this is
  a deliberate cost in exchange for the guarantee below.
- `jivl-registry` cannot execute a skill or a build even if a future
  contributor tries to add that "for convenience" — the modules capable of
  it are not reachable on its classpath, and ArchUnit fails the build if
  someone tries to change that without also changing this ADR.
- `jivl-core` must stay free of Spring/Picocli/build-tool types, which
  constrains its APIs to plain Java/records — judged an acceptable, even
  desirable, constraint for a domain model.
