# Vision

## The JVM Agent Skill Trust Problem

Agent Skills are being written for JVM ecosystems — Java, Spring, Kotlin,
Gradle-based tooling — at a pace that outstrips any human's ability to
manually vet each one. Existing Agent Skills tooling largely validates
*structure* (frontmatter, links, packaging). That is necessary but not
sufficient for a JVM audience, who additionally need to know:

- Does the Java in this skill actually compile?
- Does the skill's own Maven/Gradle project actually build and test green?
- Which JDK versions were *really exercised*, versus merely claimed?
- Does the skill declare a Spring Boot / framework version that was ever
  actually built against?
- Does using this skill measurably change an agent's output for the better,
  or is "improves your agent" an unverified marketing claim?

JIVL exists to answer these questions with evidence, not vibes.

## An Open-Source Verification Engine

JIVL is built and released as open-source software specifically so that its
rules are auditable. A verification tool that adopters cannot inspect is
asking for blind trust — which is the opposite of what verification is for.
See `GOVERNANCE.md` for the rule guaranteeing verification rule logic stays
open and auditable, and the rule that a favorable outcome can never be
purchased.

## Public and Private Verification Evidence

The MVP produces local, file-based evidence (`jivl-report.*`, `jivl-badge.*`)
usable in any context: a pull request, a CI job summary, a private registry,
or (optionally, and only with explicit maintainer review) a public listing
process like JVMskills.com. The same report format is meant to serve all of
these consumers without a separate "public" and "private" code path.

## Education and Research Possibilities

Verified, runnable Java/JVM examples are also good *teaching* material. See
`docs/academy-roadmap.md` and `docs/research-roadmap.md` for a documented,
not-yet-built, path toward verified learning packs and reproducible research
skill packs. Nothing in Phase 5 changes what Phase 0–4 already does.

## Enterprise Governance Opportunity

Organizations adopting JVM Agent Skills internally need private,
policy-governed verification — the same rule engine, running against
private repositories, with retention and audit requirements. See
`docs/enterprise-roadmap.md`. This is documentation-only in the MVP.

## Eventual Marketplace Opportunity

A marketplace for verified, properly licensed JVM skills is a plausible
long-term direction, but it introduces payment, licensing, and moderation
concerns that must not be allowed to influence verification outcomes. See
`docs/marketplace-roadmap.md`.

## Why Verification Precedes Monetization

JIVL deliberately builds and open-sources the verification engine, CLI,
reporting, and registry *before* any commercial layer exists. This ordering
matters: if verification rules and report/badge semantics are established,
tested, and public first, a later commercial offering (private hosting,
compute, retention, support) has no opportunity to quietly bend a
verification outcome to a paying customer's benefit, because the rules and
their tests already exist in the open and predate the business model.
