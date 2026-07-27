# Verification Model

## Findings, Not a Score

Every check JIVL performs produces zero or more `Finding` values. A Finding
has: a stable `ruleId` (e.g. `JIVL-SEC-001`), a `category`, a `severity`, a
`status`, a title, an explanation, evidence, an optional file/line, a
remediation, and the `verifierVersion` that produced it. JIVL intentionally
does not collapse these into one opaque numeric score. See ADR-0003.

## Statuses

| Status | Meaning |
|---|---|
| PASS | The check ran and the condition held. |
| WARN | The check ran; the condition is a soft problem, not a hard failure. |
| FAIL | The check ran and the condition was violated. |
| SKIPPED | The check could not run (missing tool, disabled by flag, etc.) — never reported as PASS. |
| ERROR | The verifier itself failed unexpectedly — never reported as PASS or hidden. |

## Severities

`INFO`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL` — orthogonal to status. A FAIL
can be LOW severity (e.g., a cosmetic naming rule) and a WARN can carry
higher informational weight; severity guides triage, status gates exit
codes.

## Categories

`STRUCTURE`, `CONTENT`, `REFERENCE`, `SECURITY`, `PROVENANCE`, `LICENSE`,
`JAVA`, `MAVEN`, `GRADLE`, `JDK_COMPATIBILITY`, `FRAMEWORK_COMPATIBILITY`,
`BUILD`, `EVALUATION`, `REPORTING`.

## Declared vs. Detected vs. Executed vs. Verified

This distinction is load-bearing throughout JIVL and is the difference
between an honest report and a fabricated one:

- **DECLARED** — a human or manifest claims something (e.g., `jivl.yaml`
  declares Java 17 as the minimum).
- **DETECTED** — static inspection found evidence consistent with a claim
  (e.g., `pom.xml` sets `maven.compiler.release` to 21).
- **EXECUTED** — a build or compilation actually ran against a specific
  JDK/framework version.
- **VERIFIED** — execution *succeeded* using that specific version. A
  version is VERIFIED only after a real, successful compilation or build
  execution on that version — never from a declaration alone.
- **UNVERIFIED** — declared or detected, but never executed successfully.
- **CONTRADICTORY** — two sources of truth (e.g., Maven and Gradle, or the
  manifest and the POM) disagree.

A report must never present an UNVERIFIED claim using VERIFIED language.

## Verification vs. Effectiveness Evaluation

Structural/security/provenance/build verification answers "is this skill
well-formed, reasonably safe to inspect, and does its Java/Maven/Gradle
project actually build on the JDKs it claims?" Effectiveness evaluation
(`jivl-evaluations`) answers a completely different question: "does using
this skill change an agent's measured output for the better?" JIVL never
lets the first answer the second — a structurally perfect, fully-verified
skill can still show `Evaluation Pending` because no agent adapter ran a
baseline-vs-with-skill comparison. See `docs/rfcs/RFC-0006-effectiveness-evaluation.md`.

## Advisory, Not a Safety Guarantee

Every generated report and badge carries language making clear that static
scanning and build execution do not prove complete safety, and that an
effectiveness claim requires both a baseline and a with-skill run to have
actually executed. See `docs/threat-model.md` for the specific limits of
what JIVL's security scanning can and cannot catch.
