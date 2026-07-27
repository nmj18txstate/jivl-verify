# ADR-0004: Narrow, Auditable Process Execution Boundaries

- **Status:** Accepted
- **Date:** 2026-07-27

## Context

Verifying a skill's Maven/Gradle project sometimes requires actually
building it — this is the only place JIVL executes another program at all.
Because the code being built is untrusted, this boundary needs to be as
narrow, explicit, and consistently tested as possible.

## Decision

- Build execution only happens when the CLI operator passes `--run-builds`
  (off by default).
- Build execution runs offline by default; `--allow-build-network` is
  required to permit dependency resolution over the network.
- Process invocation always uses `ProcessBuilder` with an explicit argument
  list — never a concatenated shell string passed to `sh -c`.
- A fixed goal/task is run (Maven `verify`; Gradle `test --no-daemon`) —
  JIVL never runs an arbitrary goal/task declared by the skill itself.
- Every invocation has an enforced timeout, bounded captured stdout/stderr,
  and an attempt to terminate descendant processes after timeout.
- `jivl-maven`/`jivl-gradle` are tested against fake/injectable process
  abstractions, never a real Maven/Gradle install, so tests stay
  deterministic and network-free.

## Alternatives Considered

- **Container/VM sandboxing of the build.** Would materially improve
  isolation but is out of scope for this MVP (no Docker/Kubernetes
  orchestration is introduced here); documented as a possible future
  hardening step, not assumed to already exist.
- **Never execute builds at all, only static POM/build-file inspection.**
  Rejected as the primary mode because "JDK compatibility" and "framework
  compatibility" are only meaningfully VERIFIED (see
  `docs/verification-model.md`) through actual execution — but static-only
  mode remains the *default* (no `--run-builds`), satisfying users who
  want zero execution risk.

## Consequences

- `--run-builds` is documented, prominently, as executing repository-
  controlled code requiring the operator's own trust judgment
  (`README.md` security warning) — this ADR does not claim to eliminate
  the inherent risk of running someone else's build, only to bound and be
  transparent about it.
