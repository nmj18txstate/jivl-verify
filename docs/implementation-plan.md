# Implementation Plan

This plan is executed phase by phase. Each phase ends with: run relevant
tests, fix failures before continuing, update
`docs/implementation-status.md`, and keep the Maven reactor buildable (or
honestly document why it cannot be built in the current environment).

| Phase | Scope | Status |
|---|---|---|
| 0 | Repository foundation, governance, documentation, RFCs, ADRs | **Done** (this pass) |
| 1 | JIVL Verify engine, JVM modules, executable CLI | Not started — see `docs/implementation-status.md` for the environment blocker |
| 2 | Reports, badges, tests, CI, GitHub Action prototype | Not started |
| 3 | JVMskills.com integration proposal and example workflow | Design/doc portion done in Phase 0 pass (`docs/jvmskills-integration.md`, RFC-0007); example workflow file pending Phase 2 CI groundwork |
| 4 | Read-only JIVL Registry website | Not started |
| 5 | JIVL Academy / JIVL Research (documentation only) | Done (documentation only, as required) |
| 6 | JIVL Enterprise / JIVL Marketplace (documentation only) | Done (documentation only, as required) |

## Sequencing Rationale

Phases 5 and 6 are documentation-only by explicit requirement and have no
build dependency, so they are completed in the same pass as Phase 0.
Phases 1–4 require actually compiling Java, running Maven/Gradle, and
executing a Spring Boot application — all of which require a JDK, a Maven
distribution, and Maven Central access to resolve dependencies. See
`docs/implementation-status.md` for the exact, current environment
verification evidence and the recommended path to complete Phases 1–4.

## What "Done" Means Per Phase

A phase is not "done" because files exist. It is done when:
- The relevant Maven modules compile.
- `./mvnw -B -ntp clean verify` passes for everything implemented so far.
- Required tests exist and pass (positive and negative fixtures for every
  new rule).
- No TODO-only files, empty modules, or mock-only demonstrations remain for
  that phase's declared scope.
- `docs/implementation-status.md` is updated with completed/remaining work,
  test counts, and the latest successful build command.
