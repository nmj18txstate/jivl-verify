# RFC-0007: JVMskills.com Integration

- **Author:** Nityam Jigyasu (@nmj18txstate)
- **Status:** Draft
- **Created:** 2026-07-27
- **Last updated:** 2026-07-28

## Summary

Define an optional, non-blocking way for JIVL's verification report to
serve as advisory input to JVMskills.com's existing, human-driven skill
review process — without modifying, forking, or assuming adoption by
JVMskills.com.

## Motivation

JVMskills.com (see `docs/competitive-landscape.md`, item 5) already
curates JVM-ecosystem AI-coding skills via maintainer PR review. That
review currently has no JVM-specific build/compatibility evidence to draw
on beyond what a human reviewer manually checks. JIVL's report is a
natural, purely optional input — never a gate.

## Goals

- Non-blocking: JIVL's report never auto-approves or auto-rejects a
  listing.
- Advisory: the maintainer's editorial judgment (usefulness, originality)
  remains the actual decision.
- Transparent and reproducible: the same report format used everywhere
  else in JIVL, not a special-cased "for JVMskills.com" format.
- Explicitly complementary, not competitive: this RFC and
  `docs/jvmskills-integration.md` are written assuming no partnership or
  endorsement exists unless and until JVMskills.com's maintainers say
  otherwise.

## Non-Goals

- Not submitting a PR to JVMskills.com as part of this work.
- Not claiming official partnership or endorsement.
- Not making the integration mandatory for any JVMskills.com submission.

## Design

See `docs/jvmskills-integration.md` for the full flow diagram, illustrative
example output, and draft outreach message. Implementation surface:
`.github/workflows/jvmskills-integration-example.yml` (an example workflow
a *submitter*, not JVMskills.com itself, could run), building on
`jivl-github-action`.

## Security Considerations

Same as any other CI use of `jivl-github-action` (ADR-0004); no new
execution surface is introduced by this proposal specifically.

## Compatibility Considerations

If JVMskills.com's maintainers request changes to the report format or
the example workflow, those changes flow through JIVL's normal RFC/report-
schema versioning process (RFC-0002), not a JVMskills.com-specific
exception.

## Alternatives

- **Fork or submit directly to JVMskills.com's repository.** Rejected per
  explicit operating constraint: no modification, fork, clone, or PR to
  JVMskills.com is performed as part of this work.
- **Make the integration mandatory/blocking.** Rejected: would turn an
  optional, advisory proposal into an unrequested gate on someone else's
  project — contrary to this RFC's own non-blocking goal.

## Open Questions

- Whether JVMskills.com's maintainers have any interest in this at all —
  genuinely unknown; the outreach message in
  `docs/jvmskills-integration.md` explicitly asks rather than assumes.

## Decision History

- 2026-07-27: Initial draft created, proposing a non-blocking, advisory
  integration with JVMskills.com's existing review process.
- 2026-07-28: Status confirmed/reset to `Draft`. This RFC was not accepted
  by the Lead Maintainer; the prior "Accepted" note above was a
  self-approval and has been corrected. Per `docs/rfcs/README.md`, only
  Nityam Jigyasu (or a future authorized maintainer) may move this RFC to
  `Accepted`, and implementation may not proceed until that happens.
  Separately and in addition to Lead Maintainer acceptance: JVMskills.com
  has not been contacted, and their feedback and any integration approval
  remain pending. Neither this RFC's Lead Maintainer acceptance nor
  JVMskills.com's own review has occurred, and both are required before
  any implementation proceeds.
