# Competitive Landscape

This document compares JIVL with existing Agent Skills tooling as of
2026-07-27. It is a factual, non-exhaustive comparison, not a claim that
JIVL is the first, only, or universally superior option. Entries were
checked against public sources at the time of writing; anything not
directly confirmed is marked **"Source verification required."**

## Landscape Summary

1. **Official Agent Skills specification and `skills-ref`** — the
   specification at agentskills.io defines the SKILL.md/frontmatter format
   and progressive-disclosure guidance, and ships a reference validation
   library (`skills-ref`) that checks frontmatter fields, naming, and
   structure. It is the specification JIVL's own structural-validation
   rules aim to be compatible with, natively in Java, without depending on
   the Python reference implementation at verification time.

2. **`agent-ecosystem/skill-validator`** — a Go CLI that validates
   spec compliance and adds content-quality checks: link resolution,
   token-count/context-budget reporting, content-density heuristics, and
   optional LLM-as-judge scoring across dimensions like clarity and
   actionability. It ships pre-commit hooks and CI examples for many agent
   platforms. It is general-purpose (not JVM-specific) and does not compile
   Java, run Maven/Gradle, or verify JDK/framework compatibility.

3. **"SkillMD"** — no single canonical project by this exact name was
   confirmed as a distinct competitor at time of writing; there are
   several unrelated `SKILL.md`-focused validators from different authors
   in the ecosystem (frontmatter/required-field checkers). **Source
   verification required** before citing a specific "SkillMD" project by
   name in any future public comparison.

4. **SkillsJars** — packages Agent Skills as JAR artifacts published to
   Maven Central, so JVM projects can depend on skills the same way they
   depend on any Maven/Gradle library (versioning, transitive deps,
   extraction plugins, Spring AI integration). SkillsJars performs a basic
   security scan before publishing but explicitly documents that this is
   not a substitute for a proper security review. It solves *packaging and
   distribution* for JVM consumers; it does not compile the skill's Java
   sources, run the skill's own Maven/Gradle build, or verify JDK/framework
   compatibility claims — which is the gap JIVL targets.

5. **JVMskills.com** (`jvm-skills/jvm-skills`) — a curated, PR-based
   directory of AI-coding-skill best-practice guides specifically for the
   JVM ecosystem, positioned against generic, often-shallow entries in
   general skill directories. Skills are contributed via fork + PR and
   reviewed by maintainers. It is a **discovery and curation** destination,
   not a verification engine — JIVL's Phase 3 integration proposal
   (`docs/jvmskills-integration.md`) is designed to be a non-blocking,
   advisory input to that human review process, not a replacement for it.

6. **General Agent Skills registries** (e.g. general skill directories and
   install-tooling such as npm/CLI-based skill installers) — focus on
   discovery, installation, and cross-agent compatibility, generally
   language-agnostic. They do not verify JVM build/compatibility claims.

7. **General skill security scanners** — several tools and academic studies
   perform static or dataset-scale scanning of skill repositories for
   malicious patterns. JIVL's `jivl-security` rules are in the same family
   (deterministic static detection with evidence and remediation) but are
   JVM-context-aware and are explicitly documented as best-effort, not a
   safety proof (see `docs/threat-model.md`).

8. **Skill effectiveness/evaluation tooling** — LLM-as-judge scoring (as in
   `skill-validator`) evaluates skill *content* quality. JIVL's evaluation
   model (`jivl-evaluations`) is narrower and stricter: it requires an
   actual baseline-vs-with-skill agent run through a provider adapter
   before it will report anything other than "Evaluation Pending" — it
   never derives an effectiveness claim from content quality alone.

## Comparison Matrix

| Capability | Agent Skills spec / `skills-ref` | `skill-validator` | SkillsJars | JVMskills.com | JIVL |
|---|---|---|---|---|---|
| Structural validation | Yes (reference impl.) | Yes | Partial (packaging-time) | No (curation, not validation) | Yes (native Java) |
| Frontmatter validation | Yes | Yes | Partial | No | Yes |
| Link validation | Partial | Yes | No | No | Yes |
| Content-quality checks | No | Yes (density, tokens, LLM-judge) | No | Editorial (human review) | No (out of scope by design) |
| Static security checks | No | No | Basic pre-publish scan | No | Yes (deterministic, JVM-aware) |
| Provenance checks | No | No | Publisher = Maven Central identity | Maintainer review | Yes (`jivl.yaml` manifest) |
| Packaging and distribution | No | No | Yes (Maven Central JARs) | Directory listing only | No (out of scope) |
| Public discovery | Via spec ecosystem | No | Via Maven Central search | Yes (its core purpose) | Optional, via registry or JVMskills.com evidence |
| Java compilation | No | No | No | No | Yes |
| Maven verification | No | No | Packaging-only | No | Yes (build execution, opt-in) |
| Gradle verification | No | No | No | No | Yes (build execution, opt-in) |
| JDK compatibility (verified, not just declared) | No | No | No | No | Yes |
| Spring/JVM framework compatibility | No | No | Via Spring AI integration example | No | Yes |
| With-skill vs. without-skill evaluation | No | Content-quality scoring only | No | No | Yes (model only; requires an agent adapter to execute) |
| Hosted attestations | No | No | Maven Central hosting itself | No | Not in MVP (documented roadmap only) |
| Private enterprise governance | No | No | No | No | Not in MVP (documented roadmap only) |

## Research Checklist (for future updates to this document)

- [ ] Re-verify `skill-validator`'s current feature set before each JIVL
      release (Go CLI, actively developed).
- [ ] Re-verify SkillsJars' current security-scan scope and Spring AI
      integration status.
- [ ] Re-verify JVMskills.com's current contribution/review process.
- [ ] Confirm or retract the "SkillMD" entry once/if a specific project by
      that name is identified with a citable source.
- [ ] Re-check for any project that adds JVM build verification (Maven/
      Gradle execution, JDK compatibility) since this document was written,
      since that is JIVL's core differentiation claim.
