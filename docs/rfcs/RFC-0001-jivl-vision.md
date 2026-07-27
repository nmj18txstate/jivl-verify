# RFC-0001: JIVL Vision

- **Author:** Nityam Jigyasu (@nmj18txstate)
- **Status:** Accepted
- **Created:** 2026-07-27
- **Last updated:** 2026-07-27

## Summary

Establish JIVL's founding thesis: JVM-native verification evidence for
Agent Skills, combining structural/security/provenance checks with actual
Java compilation, Maven/Gradle build execution, and a non-fabricating
effectiveness-evaluation model.

## Motivation

Existing Agent Skills tooling (see `docs/competitive-landscape.md`)
validates structure, curates listings, or packages skills for
distribution, but nothing found actually compiles the Java, runs the
skill's own Maven/Gradle build, or distinguishes a *declared* JDK/framework
version from a *verified* one. JVM developers adopting skills have no
JVM-specific evidence to go on.

## Goals

- Produce categorized, evidence-backed findings (not a single score) for
  JVM-focused skills.
- Make the DECLARED/DETECTED/EXECUTED/VERIFIED distinction
  (`docs/verification-model.md`) a first-class, enforced concept.
- Keep verification rule logic open source and auditable.
- Never let static checks claim to prove complete safety, and never let
  content quality alone substitute for an executed effectiveness
  evaluation.

## Non-Goals

- Not a general-purpose, language-agnostic skill validator (that space is
  already served — see item 2 in `docs/competitive-landscape.md`).
- Not a skill marketplace, payment platform, or hosted attestation service
  in this MVP.
- Not a claim of official standing with OpenJDK, Oracle, Spring, Agent
  Skills, Anthropic, OpenAI, GitHub, SkillsJars, or JVMskills.com.

## Design

See `docs/vision.md` and `docs/architecture.md` for the full design;
this RFC exists to record the founding decision to pursue this thesis at
all, and to anchor later RFCs (0002-0007) as elaborations of it.

## Security Considerations

Establishing "verification evidence" as the product means the security
model (`docs/threat-model.md`) has to be trustworthy from the start —
JIVL inspecting untrusted skill content is the central security concern of
the whole project, not an afterthought.

## Compatibility Considerations

None yet — this is the founding RFC; compatibility concerns begin to
matter starting with RFC-0002 (report model) since reports are the
long-lived, potentially-consumed-by-others artifact.

## Alternatives

- **Be a general skill validator with a JVM plugin.** Rejected: JVM-
  specific concerns (Maven/Gradle execution, JDK/framework compatibility)
  are deep enough to deserve a purpose-built tool rather than a plugin atop
  a language-agnostic core.
- **Focus only on security scanning, skip build verification entirely.**
  Rejected: build verification is exactly the differentiated capability
  nothing else in the landscape currently provides.

## Open Questions

- Whether a GraalVM native-image distribution is worth the added build
  complexity once the JVM `.jar` distribution has real adoption evidence
  (see `README.md` roadmap note).
- Whether additional JVM frameworks (Quarkus, Micronaut) should be added
  before or after the first tagged release.

## Decision History

- 2026-07-27: Accepted as the founding thesis for the MVP scope described
  in `docs/implementation-plan.md`.
