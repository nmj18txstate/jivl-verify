# JVMskills.com Integration Proposal (Optional, Non-Blocking)

**Status: proposal only.** JIVL has not modified, forked, cloned, or
submitted a pull request to JVMskills.com, and this document does not
assume JVMskills.com's approval or adoption of anything described here.

## Why

JVMskills.com curates AI-coding-skill best-practice guides for the JVM
ecosystem via maintainer review of contributor PRs (see
`docs/competitive-landscape.md`, item 5). That review is editorial —
usefulness, originality, fit for the directory. JIVL's JVM-native build/
compatibility evidence is a natural, optional *input* to that review, not a
replacement for it.

## Proposed Flow

```
JVMskills submission
        |
        v
JIVL GitHub Action
        |
        v
Non-blocking verification report
        |
        v
Maintainer reviews:
  - Skill usefulness and originality
  - JVM verification evidence
```

1. A contributor submits a JVM skill through JVMskills.com's existing
   contribution process (fork -> add skill -> PR), unchanged.
2. JIVL verifies the referenced skill repository or directory (structure,
   security, provenance, Java compilation, Maven/Gradle build, JDK/
   framework compatibility) via the `jivl-github-action` composite action.
3. JIVL generates a Markdown summary attached to the PR/CI run.
4. The JSON report is retained as a CI artifact for anyone who wants the
   full detail.
5. The JVMskills.com maintainer reviews usefulness, originality, JVM
   accuracy, provenance, and the JIVL evidence together.
6. JIVL does not automatically approve or reject the listing.
7. JVMskills.com maintainers may later define explicit acceptance policies
   after gaining experience with the evidence JIVL produces — nothing here
   presumes that outcome.

## Properties This Proposal Must Preserve

Optional, non-blocking, advisory, reproducible, transparent, compatible
with human review, independent of a single numeric score, and explicitly
complementary to (not competing with) JVMskills.com's own review process.

## Illustrative Example Output

```
JIVL Verification: PASS WITH WARNINGS

[x] Agent Skills structure valid
[x] Java 21 examples compile
[x] Maven tests pass
[x] No likely embedded secrets detected
[!] Java 17 compatibility was not executed
[!] Provenance manifest is incomplete
[ ] Agent effectiveness evaluation is pending

This report is advisory and does not replace JVMskills.com maintainer review.
```

## Draft Outreach Message

> Subject: JIVL — an optional, non-blocking JVM verification report for
> JVMskills.com submissions
>
> Hi — I'm Nityam Jigyasu (@nmj18txstate), and I've been building JIVL, an
> open-source, JVM-focused verification layer for Agent Skills (structural
> validation, static security scanning, provenance checks, Java
> compilation, and Maven/Gradle build/compatibility evidence). It's an
> early prototype, not a finished product.
>
> I think it could be a useful, purely optional input to JVMskills.com's
> existing review process: a GitHub Action that runs against a submitted
> skill and posts a non-blocking Markdown summary plus a JSON artifact,
> without changing anything about how you evaluate usefulness or
> originality. It doesn't gate merges, and it isn't asking for adoption —
> I'd just value your feedback on whether the evidence it produces is
> actually useful signal from your side of the review, and what you'd want
> changed if so.
>
> No pressure either way, and thank you for building and maintaining
> JVMskills.com — happy to share the report format or a sample run if
> that's useful.
>
> — Nityam

See `docs/rfcs/RFC-0007-jvmskills-integration.md` for the design-level RFC
and `.github/workflows/jvmskills-integration-example.yml` for the example
workflow.
