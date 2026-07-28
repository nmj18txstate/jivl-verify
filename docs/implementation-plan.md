# Implementation Plan

This plan is executed phase by phase. Each phase ends with: run relevant
tests, fix failures before continuing, update
`docs/implementation-status.md`, and keep the Maven reactor buildable (or
honestly document why it cannot be built in the current environment).

| Phase | Scope | Status |
|---|---|---|
| 0 | Repository foundation, governance, documentation, RFCs, ADRs, Maven reactor | See gate checklist below — see `docs/implementation-status.md` for current evidence |
| 1 | JIVL Verify engine, JVM modules, executable CLI | Not started |
| 2 | Reports, badges, tests, CI, GitHub Action prototype | Not started |
| 3 | JVMskills.com integration proposal and example workflow | Design/doc portion done in Phase 0 pass (`docs/jvmskills-integration.md`, RFC-0007, currently `Draft`); example workflow file pending Phase 2 CI groundwork |
| 4 | Read-only JIVL Registry website | Not started |
| 5 | JIVL Academy / JIVL Research (documentation only) | Done (documentation only, as required) |
| 6 | JIVL Enterprise / JIVL Marketplace (documentation only) | Done (documentation only, as required) |

## Phase 0 Gate Checklist

Phase 0 is complete only when every gate below is checked, with evidence in
`docs/implementation-status.md`. A gate may not be checked based on intent
or partial progress — only on verified, current repository state.

- [ ] Root Java 21 Maven reactor exists (`pom.xml`, packaging `pom`,
      `maven.compiler.release=21`).
- [ ] Root `pom.xml` centralizes plugin management (Compiler, Surefire,
      Enforcer) with pinned, non-snapshot versions.
- [ ] Maven Wrapper present and structurally valid: `mvnw`, `mvnw.cmd`,
      `.mvn/wrapper/maven-wrapper.properties` (and
      `maven-wrapper.jar`/`MavenWrapperDownloader` if the selected wrapper
      type requires it), generated via an installed Maven or another
      verified official mechanism — never hand-fabricated.
- [ ] Every intended Maven module (`jivl-core`, `jivl-security`,
      `jivl-maven`, `jivl-gradle`, `jivl-reporting`, `jivl-evaluations`,
      `jivl-cli`, `jivl-registry`, `jivl-test-fixtures`) has a valid,
      dependency-minimal `pom.xml` and is declared as a `<module>` in the
      root reactor.
- [ ] Module dependency directions are documented (`AGENTS.md`,
      `docs/architecture.md`) and no module POM violates them.
- [ ] Governance and contributor documents are present and accurate:
      `AGENTS.md`, `GOVERNANCE.md`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`,
      `SECURITY.md`, `CITATION.cff`.
- [ ] RFC process (`docs/rfcs/README.md`, RFC-0001..0007) and ADR process
      (`docs/adr/`) are present, with RFCs in an honest `Draft` state (none
      self-approved).
- [ ] `.github/workflows/ci.yml` exists, triggers on PRs and pushes to
      `main`, uses `contents: read` permissions, Temurin Java 21, and runs
      `./mvnw -B -ntp clean verify`.
- [ ] The complete reactor builds successfully via the Maven Wrapper:
      `mvnw.cmd -B -ntp clean verify` (Windows) /
      `./mvnw -B -ntp clean verify` (Unix), with the exact command, exact
      result, and exact environment recorded in
      `docs/implementation-status.md`.
- [ ] `docs/implementation-status.md` reflects only actually-verified
      evidence — no claimed test count, build result, or module status that
      was not directly observed in this environment.
- [ ] No Phase 1+ feature (verification rules, CLI commands, report
      generation, registry endpoints, evaluation logic) is implemented or
      claimed as implemented under the Phase 0 label.

Phase 0 may be marked **Complete** in `docs/implementation-status.md` only
once every box above is checked with direct evidence.

## Sequencing Rationale

Phases 5 and 6 are documentation-only by explicit requirement and have no
build dependency, so their documentation is completed alongside Phase 0.
Phases 1–4 require actually compiling Java, running Maven/Gradle, and
executing a Spring Boot application against real production code — none of
which is in scope for Phase 0, which only needs to prove the reactor itself
builds. See `docs/implementation-status.md` for the exact, current
environment verification evidence.

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
