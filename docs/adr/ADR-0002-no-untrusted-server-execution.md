# ADR-0002: The Registry Website Never Executes Anything

- **Status:** Accepted
- **Date:** 2026-07-27

## Context

`jivl-registry` is a public-facing (or at least multi-user-facing) web
application rendering verification reports. Skill directories, by design,
contain untrusted, potentially adversarial content. A web application that
could be induced to execute a skill's build, script, or SKILL.md content
would turn a read-only reporting tool into a remote-code-execution vector.

## Decision

`jivl-registry` never executes a skill, never runs Maven or Gradle, never
runs an arbitrary script, and never fetches a remote URL found in report
data. It only reads pre-generated, schema-validated JSON reports from
`registry-data/` and renders them. This is enforced architecturally (ADR-
0001/ADR-0006: `jivl-registry` cannot depend on `jivl-cli`, `jivl-maven`,
or `jivl-gradle`), not merely by convention.

## Alternatives Considered

- **"Verify on demand" from the registry UI** (upload a skill, get a live
  report). Rejected for this MVP: it would require the registry to invoke
  exactly the execution paths this ADR exists to keep out of a
  multi-user-facing web process. Could be revisited later as a fully
  separate, sandboxed service — never inside `jivl-registry` itself.

## Consequences

- The registry can only ever show what a CLI/CI run already produced and
  placed into `registry-data/`; it cannot be a self-service verification
  portal in this architecture.
- This significantly reduces the registry's own attack surface and blast
  radius, at the cost of requiring an out-of-band step (CLI or CI) to
  populate `registry-data/`.
