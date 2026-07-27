# Architecture

## Module Dependency Graph

```
                     +------------+
                     | jivl-core  |
                     +------------+
                        ^  ^  ^  ^
        +---------------+  |  |  +------------------+
        |                  |  |                     |
+---------------+  +---------------+  +----------------+  +------------------+
| jivl-security |  |  jivl-maven   |  |  jivl-gradle   |  |  jivl-reporting  |
+---------------+  +---------------+  +----------------+  +------------------+
                                                                    ^
                                                                    |
                                                          +--------------------+
                                                          | jivl-evaluations   |
                                                          +--------------------+

jivl-cli        --> jivl-core, jivl-security, jivl-maven, jivl-gradle,
                     jivl-reporting, jivl-evaluations
jivl-registry    --> jivl-core, jivl-reporting            (never jivl-cli)
jivl-github-action -> invokes the built jivl-cli.jar; no verification logic
jivl-test-fixtures -> test-scope only; no production module depends on it
```

`jivl-core` has no dependency on Spring, Picocli, or any build-tool
integration — it defines the domain model (findings, statuses, severities,
categories, environment model, skill identity, safe path utilities, a
`Clock` abstraction) and the orchestration *contracts* (interfaces) that
other modules implement. This keeps the domain model reusable by a CLI, a
Spring Boot web app, or a future IDE plugin without dragging a web framework
into all of them.

## Why This Boundary (ADR-0001)

The alternative — a single monolithic module — would let `jivl-registry`
accidentally depend on `jivl-cli` internals, or let Spring leak into
`jivl-core`'s domain types. Multi-module Maven plus ArchUnit boundary tests
turn "please don't do that" into a build failure. See
`docs/adr/ADR-0001-modular-architecture.md`.

## Verification Orchestration

`jivl-core` defines a `VerificationRule` contract and a `Finding` model.
Each concrete module (`jivl-security`, `jivl-maven`, `jivl-gradle`) provides
rule implementations; `jivl-reporting` renders whatever findings the
orchestrator collected; `jivl-cli` is the composition root that wires rules,
runs them against a skill directory, and hands the resulting findings to
`jivl-reporting`. No module reaches "sideways" into another module's
implementation details — only through `jivl-core` contracts.

## Process Execution Boundary (ADR-0004)

Maven and Gradle build execution is the one place JIVL runs another
program. This is deliberately isolated behind narrow interfaces in
`jivl-maven`/`jivl-gradle` (`ProcessBuilder`, argument lists, timeouts,
bounded output, offline-by-default) so the "we execute a subprocess" surface
area is small, auditable, and consistently tested with fake process
abstractions rather than real Maven/Gradle installs.

## Report vs. Score (ADR-0003)

JIVL deliberately does not produce one opaque numeric score. A report is a
set of categorized findings with statuses (PASS/WARN/FAIL/SKIPPED/ERROR) and
severities. This is slower to eyeball than "87/100" but does not hide *why*
a skill failed, and does not invite gaming a single number. See
`docs/verification-model.md`.

## Registry Is Read-Only By Construction

`jivl-registry` depends only on `jivl-core` and `jivl-reporting`. It cannot
depend on `jivl-cli`, `jivl-maven`, or `jivl-gradle` — there is no code path
by which the registry could execute a build or a skill, because the modules
capable of doing that are not on its classpath. ArchUnit enforces this at
build time. See ADR-0006.
