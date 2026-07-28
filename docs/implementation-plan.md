# Implementation Plan

This plan is executed phase by phase. Each phase ends with: run relevant
tests, fix failures before continuing, update
`docs/implementation-status.md`, and keep the Maven reactor buildable (or
honestly document why it cannot be built in the current environment).

| Phase | Scope | Status |
|---|---|---|
| 0 | Repository foundation, governance, documentation, RFCs, ADRs, Maven reactor | **Complete** (merged via PR #1) — see Phase 0 gate checklist below and `docs/implementation-status.md` |
| 1 | JIVL Verify engine, JVM modules, executable CLI | Phase 1A **authorized** (RFC-0001 and RFC-0008 `Accepted`) but **not started** — see Phase 1A-1D gate checklist below. Phase 1B/1C depend on 1A; Phase 1D still blocked on a not-yet-existing CLI RFC |
| 2 | Reports, badges, tests, CI, GitHub Action prototype | Not started |
| 3 | JVMskills.com integration proposal and example workflow | Design/doc portion done in Phase 0 pass (`docs/jvmskills-integration.md`, RFC-0007, currently `Draft`); example workflow file pending Phase 2 CI groundwork |
| 4 | Read-only JIVL Registry website | Not started |
| 5 | JIVL Academy / JIVL Research (documentation only) | Done (documentation only, as required) |
| 6 | JIVL Enterprise / JIVL Marketplace (documentation only) | Done (documentation only, as required) |

## Phase 0 Gate Checklist

Phase 0 is complete only when every gate below is checked, with evidence in
`docs/implementation-status.md`. A gate may not be checked based on intent
or partial progress — only on verified, current repository state.

- [x] Root Java 21 Maven reactor exists (`pom.xml`, packaging `pom`,
      `maven.compiler.release=21`).
- [x] Root `pom.xml` centralizes plugin management (Compiler, Surefire,
      Enforcer) with pinned, non-snapshot versions.
- [x] Maven Wrapper present and structurally valid: `mvnw`, `mvnw.cmd`,
      `.mvn/wrapper/maven-wrapper.properties` (and
      `maven-wrapper.jar`/`MavenWrapperDownloader` if the selected wrapper
      type requires it), generated via an installed Maven or another
      verified official mechanism — never hand-fabricated. `mvnw` is
      committed with the executable bit set (mode `100755`; see commit
      `ecb111d`), which CI confirmed by actually invoking `./mvnw` on
      Ubuntu.
- [x] Every intended Maven module (`jivl-core`, `jivl-security`,
      `jivl-maven`, `jivl-gradle`, `jivl-reporting`, `jivl-evaluations`,
      `jivl-cli`, `jivl-registry`, `jivl-test-fixtures`) has a valid,
      dependency-minimal `pom.xml` and is declared as a `<module>` in the
      root reactor.
- [x] Module dependency directions are documented (`AGENTS.md`,
      `docs/architecture.md`) and no module POM violates them.
- [x] Governance and contributor documents are present and accurate:
      `AGENTS.md`, `GOVERNANCE.md`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`,
      `SECURITY.md`, `CITATION.cff`.
- [x] RFC process (`docs/rfcs/README.md`, RFC-0001..0007) and ADR process
      (`docs/adr/`) are present, with RFCs in an honest `Draft` state (none
      self-approved) — both the `Status` field and each RFC's Decision
      History now agree that none has been accepted.
- [x] `.github/workflows/ci.yml` exists, triggers on PRs and pushes to
      `main`, uses `contents: read` permissions, Temurin Java 21, and runs
      `./mvnw -B -ntp clean verify`.
- [x] The complete reactor builds successfully via the Maven Wrapper:
      `mvnw.cmd -B -ntp clean verify` (Windows) /
      `./mvnw -B -ntp clean verify` (Unix), with the exact command, exact
      result, and exact environment recorded in
      `docs/implementation-status.md`. This is now also confirmed by an
      actual GitHub Actions run on Temurin JDK 21 (commit `ecb111d`, check
      run `build`, `conclusion: success`,
      https://github.com/nmj18txstate/jivl-verify/actions/runs/30379640313/job/90344021349),
      not just the local Windows run.
- [x] `docs/implementation-status.md` reflects only actually-verified
      evidence — no claimed test count, build result, or module status that
      was not directly observed in this environment.
- [x] No Phase 1+ feature (verification rules, CLI commands, report
      generation, registry endpoints, evaluation logic) is implemented or
      claimed as implemented under the Phase 0 label.

All gates above are checked based on direct evidence: the local Windows
Maven Wrapper build (`docs/implementation-status.md`) and the real GitHub
Actions CI run on Temurin JDK 21 linked above, which exercises the Unix
wrapper (`./mvnw`) end to end rather than only structurally. Phase 0 is
recorded as **Complete** in `docs/implementation-status.md` on this basis.

## Phase 1 Gate Checklist

**RFC status (updated 2026-07-28 by explicit Lead Maintainer governance
decision):**
- **RFC-0001** (JIVL Vision) — **Accepted** by Nityam Jigyasu
  (@nmj18txstate), Lead Maintainer. Authorizes continued planning and
  incremental implementation of the founding thesis; does not by itself
  authorize any specific module's detailed design.
- **RFC-0002** (Verification Report Model) — **remains `Draft`**, not
  partially accepted. Gates all Phase 2 report-envelope work: JSON
  top-level fields, `schemaVersion`, `reportDigest` and its excluded-field
  list, and the environment-summary field list (still unresolved). Will be
  reconsidered no earlier than Phase 2 planning.
- **RFC-0008** (Core Findings and Verification Contracts) — **Accepted**
  by Nityam Jigyasu (@nmj18txstate), Lead Maintainer. Extracted from
  RFC-0002's Finding-domain portion; this is what Phase 1A implements.

**Phase 1A is therefore authorized to begin implementation** (RFC-0001 and
RFC-0008 are both `Accepted`). Phase 1B and 1C depend on Phase 1A's
contracts and cannot start before 1A is done. **Phase 1D remains blocked**
on a CLI-specific RFC that does not yet exist. See
`docs/rfcs/PHASE-1-RFC-REVIEW.md` for the readiness review that led to this
decision, and RFC-0008 and RFC-0002's Decision History for the exact
governance record. **No gate below is checked complete in this pass** —
authorization to begin is not the same as evidence of completion; Phase 1A
gates are checked only once implemented, tested, and verified per
`docs/implementation-status.md`.

### Phase 1A — Core findings and verification contracts (`jivl-core`)

**Authorized by:** RFC-0001 (Accepted) and
[RFC-0008](rfcs/RFC-0008-core-findings-and-verification-contracts.md)
(Accepted). RFC-0008 is the authoritative design reference for this
sub-phase's scope — the bullet list below summarizes it; where the two
differ, RFC-0008 governs.

**Scope:**
- [ ] `FindingStatus` (`PASS`/`WARN`/`FAIL`/`SKIPPED`/`ERROR`, per
      `docs/verification-model.md`)
- [ ] `FindingSeverity` (`INFO`/`LOW`/`MEDIUM`/`HIGH`/`CRITICAL`)
- [ ] `FindingCategory` (`STRUCTURE`/`CONTENT`/`REFERENCE`/`SECURITY`/
      `PROVENANCE`/`LICENSE`/`JAVA`/`MAVEN`/`GRADLE`/
      `JDK_COMPATIBILITY`/`FRAMEWORK_COMPATIBILITY`/`BUILD`/`EVALUATION`/
      `REPORTING`)
- [ ] Stable `RuleId` value type (validated wrapper, e.g. for
      `JIVL-SEC-001`-shaped identifiers)
- [ ] Safe relative-path value type (rejects `../` traversal, absolute
      paths, and symlink escape outside a skill root, per
      `docs/threat-model.md`)
- [ ] Redacted evidence value type (evidence must not itself become a
      secret-leak vector, per `docs/threat-model.md`'s "Secrets Leak
      Through JIVL's Own Output")
- [ ] Immutable `Finding` model (record): `ruleId`, `category`, `severity`,
      `status`, `title`, `explanation`, `evidence`, optional `file`/`line`,
      `remediation`, `verifierVersion`
- [ ] Deterministic finding ordering (documented, stable sort — not
      insertion or hash order)
- [ ] `VerificationSummary`/count aggregation (per-status counts; no single
      aggregate score, per ADR-0003)
- [ ] `VerificationRule` contract (interface every rule-producing module
      will implement)
- [ ] `VerificationContext` with `Clock` injection (per `AGENTS.md`)
- [ ] `jivl-core` remains framework-free: no Spring, no Picocli, no
      build-tool types (per `AGENTS.md`, ADR-0001)

**Explicitly out of scope for 1A:** the full JSON report envelope
(`schemaVersion`, `reportId`, `reportDigest`, environment summary, other
RFC-0002 top-level report fields), the badge model, the CLI, the registry,
and Maven/Gradle execution. Phase 1A is the `Finding` domain model and
rule-execution contracts only — see `docs/rfcs/PHASE-1-RFC-REVIEW.md` for
why this split is recommended.

**Implementation requirement:** All types live in `jivl-core` only, as
immutable records/enums, following `AGENTS.md`'s Java style rules
(constructor injection, no field injection, no wildcard imports, no static
mutable global state, no Lombok, no Java preview features, no
`System.out`).

**Required tests:** Positive and negative unit tests for every value type
(e.g. the safe relative-path type rejects `../etc/passwd` and accepts
`docs/foo.md`; the redacted-evidence type actually redacts a recognizable
secret pattern); a test asserting `Finding` ordering is deterministic
across repeated runs with shuffled input; a test asserting no code path
calls `Instant.now()` directly instead of using the injected `Clock`.

**Maven verification command:**
```
.\mvnw.cmd -B -ntp -pl jivl-core -am clean verify
```
(or the full reactor: `.\mvnw.cmd -B -ntp clean verify`).

**Documentation update requirement:** `docs/implementation-status.md`
updated with the new test count and what `jivl-core` now contains;
`docs/verification-model.md` updated only if the implemented
Status/Severity/Category set deviates from what is documented there
(it should not, absent a new RFC).

**Evidence needed before checking the box:** A green `clean verify` run
(exact command and result recorded in `docs/implementation-status.md`),
the exact passing test count, and confirmation (POM inspection) that
`jivl-core`'s `pom.xml` still declares no Spring/Picocli/build-tool
dependency.

### Phase 1B — Skill identity and structural inspection

**Scope:**
- [ ] Safe skill-root normalization (canonicalize and verify no traversal
      outside the declared skill root)
- [ ] Required `SKILL.md` discovery
- [ ] Frontmatter extraction as data only (never interpreted as
      instructions, never executed)
- [ ] Required field validation
- [ ] Directory/name consistency checks
- [ ] Structural findings (emitted via the Phase 1A `Finding`/
      `VerificationRule` contracts)
- [ ] Original valid and invalid fixtures, purpose-built for these tests
- [ ] No execution of skill instructions (per `AGENTS.md`'s Security
      Constraints and `docs/threat-model.md`)

**Implementation requirement:** Depends only on Phase 1A's contracts.
Frontmatter parsing must never invoke a general-purpose parser path that
could evaluate embedded logic — data extraction only, never execution.

**Required tests:** A positive fixture (well-formed skill directory)
produces the expected PASS findings; negative fixtures (missing
`SKILL.md`, malformed frontmatter, directory/name mismatch, a `../`
traversal attempt) each produce the expected FAIL/WARN findings with
correct `ruleId`/`category`. At least one fixture specifically exercises
the safe-path rejection from `docs/threat-model.md`.

**Maven verification command:**
```
.\mvnw.cmd -B -ntp clean verify
```

**Documentation update requirement:** `docs/implementation-status.md`
updated with new module/test counts; any new `ruleId`s introduced must be
recorded there even though a full rule catalog remains a Phase 2+
reporting concern.

**Evidence needed before checking the box:** Green `clean verify`, passing
positive and negative fixture tests, and a manual confirmation (e.g. a
grep for `ProcessBuilder`/`Runtime.exec`/reflection-based invocation) that
no fixture or code path executes skill-declared content.

### Phase 1C — Verification orchestration

**Scope:**
- [ ] Deterministic rule execution (fixed, documented order)
- [ ] Rule failure isolation (one rule throwing does not abort the others)
- [ ] ERROR versus FAIL behavior (a rule that fails *to run* reports
      ERROR, never FAIL and never a silently swallowed exception, per
      `AGENTS.md`'s Prohibited Shortcuts and `docs/verification-model.md`)
- [ ] Stable output ordering (documented, reproducible)
- [ ] Clock-based timestamps (reusing Phase 1A's `VerificationContext`)
- [ ] Aggregate result construction (using Phase 1A's
      `VerificationSummary`)
- [ ] No report rendering yet — text/Markdown/JSON output is Phase 2/
      `jivl-reporting`; 1C stops at an in-memory result object

**Implementation requirement:** An orchestrator that accepts a list of
`VerificationRule`s and a `VerificationContext`, executes them all,
isolates per-rule failures, and returns a deterministic, ordered result.
No dependency on `jivl-reporting`, `jivl-cli`, or `jivl-registry`.

**Required tests:** A throwing rule does not prevent other rules from
running and is surfaced as an ERROR finding, not silently dropped; two
runs with the same rule set, context, and injected `Clock` produce
identical ordering; a zero-rule run produces a valid, well-formed empty
result rather than a null or exception.

**Maven verification command:**
```
.\mvnw.cmd -B -ntp clean verify
```

**Documentation update requirement:** `docs/implementation-status.md`
updated; `docs/architecture.md`'s "Verification Orchestration" section
reviewed for accuracy against what was actually built.

**Evidence needed before checking the box:** Green `clean verify`; a
specific test demonstrating rule-failure isolation with its result
recorded; a specific test demonstrating deterministic ordering across
repeated runs.

### Phase 1D — Initial CLI composition

**Scope:**
- [ ] Only after applicable CLI RFC decisions — no CLI-flag or
      command-surface RFC currently exists; per `AGENTS.md`, "A change to
      ... CLI flags ... requires an RFC," so this gate cannot be scoped
      further until that RFC exists and goes through the same review this
      document applies to RFC-0001/RFC-0002
- [ ] No Phase 2 badge/report work
- [ ] No registry
- [ ] No Maven/Gradle child-process execution unless separately approved
      (ties to `docs/threat-model.md`'s `--run-builds` boundary and
      ADR-0004 — needs its own explicit sign-off)

**Implementation requirement:** Not yet determinable. Intentionally left
underspecified pending a CLI-specific RFC; filling in an implementation
requirement now would preempt that RFC process.

**Required tests:** To be defined alongside the CLI RFC.

**Maven verification command:** To be defined alongside the CLI RFC
(expected to remain `.\mvnw.cmd -B -ntp clean verify` for the full
reactor).

**Documentation update requirement:** A new RFC under `docs/rfcs/`
covering CLI flags/command surface must exist and reach `Accepted` before
this gate can be meaningfully detailed further; this document should then
be updated with real sub-gates, replacing this placeholder.

**Evidence needed before checking the box:** An `Accepted` CLI RFC, then
the same evidence pattern as 1A-1C once that RFC defines what to build.

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
