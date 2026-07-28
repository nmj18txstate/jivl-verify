# RFC-0003: JVM Compatibility Metadata

- **Author:** Nityam Jigyasu (@nmj18txstate)
- **Status:** Draft
- **Created:** 2026-07-27
- **Last updated:** 2026-07-27

## Summary

Define how JIVL models and reports JDK and JVM-framework compatibility,
centered on the DECLARED/DETECTED/EXECUTED/VERIFIED/UNVERIFIED/
CONTRADICTORY distinction.

## Motivation

"Works on Java 17+" is a common, often unverified claim. JIVL's core value
proposition depends on never repeating that mistake itself — a claim of
compatibility must be traceable to whether it was ever actually executed.

## Goals

- A JDK version is `VERIFIED` only after a real, successful compilation or
  build execution using that specific version.
- Detect contradictions: manifest vs. POM vs. build.gradle disagreements,
  or a manifest claiming a minimum Java version lower than what detected
  source features actually require.
- Extend the same model to JVM frameworks (Spring Boot, Spring Framework
  when directly declared, Kotlin JVM Plugin, Resilience4j when directly
  declared, JUnit, Jakarta Servlet API), via extension interfaces so more
  detectors can be added later without touching orchestration.

## Non-Goals

- Not attempting universal compatibility inference from a single
  successful JDK run (one green build on JDK 21 does not imply JDK 17
  compatibility).
- Not implementing a general dependency-license scanner (see
  `docs/provenance-policy.md`, "Relationship to Licensing Checks").

## Design

- Compatibility findings carry one of: `DECLARED`, `DETECTED`, `EXECUTED`,
  `VERIFIED`, `UNVERIFIED`, `CONTRADICTORY` (see
  `docs/verification-model.md`).
- Multiple `--jdk-home` entries let the CLI run Maven/Gradle verification
  once per requested JDK, each time setting `JAVA_HOME`/`PATH` only for the
  child process, never mutating the host environment.
- Framework detectors implement a shared extension interface (in
  `jivl-maven`/`jivl-gradle`, invoked by `jivl-cli`) so a new framework
  detector is additive, not a change to core orchestration.
- Framework compatibility is `VERIFIED` only when: the version was
  detected or declared, the relevant project actually built/tested
  successfully, and the report records which Java environment was used.

## Security Considerations

Running the same build across multiple `--jdk-home` values multiplies
process-execution surface area proportionally; the same ADR-0004
boundaries (timeout, bounded output, argument-list ProcessBuilder) apply
per invocation, not just once.

## Compatibility Considerations

Adding a new framework detector must not change the meaning of existing
`FRAMEWORK_COMPATIBILITY` findings for frameworks already supported —
additive only, per the extension-interface design.

## Alternatives

- **Trust declared compatibility ranges outright.** Rejected: this is
  precisely the gap in the existing landscape (see
  `docs/competitive-landscape.md`) that JIVL exists to close.

## Open Questions

- How deep static Java-language-feature detection should go for inferring
  a *minimum* Java level from source (only "reasonably deterministic"
  cases per the project brief — exact feature list to be finalized during
  Phase 1 implementation, not guessed at here).

## Decision History

- 2026-07-27: Accepted; implementation deferred to Phase 1.
