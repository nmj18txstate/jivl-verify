# Roadmap

## Near Term (Phases 1-4, this MVP's implementation scope)

1. **JIVL Verify engine + CLI** — structural validation, security scanning,
   provenance manifest validation, Java compilation, Maven/Gradle
   detection and opt-in build execution, JDK/framework compatibility
   evidence, evaluation-spec parsing.
2. **Reports, badges, tests, CI** — text/Markdown/JSON reports, SHA-256
   report digesting, local SVG badges, comprehensive unit/integration
   tests, GitHub Actions CI + CodeQL, `jivl-github-action` prototype.
3. **JVMskills.com integration proposal** — non-blocking, advisory,
   optional (see `docs/jvmskills-integration.md`).
4. **Read-only JIVL Registry** — Spring Boot + Thymeleaf website and JSON
   API over file-backed reports, with no execution capability whatsoever.

## Documented, Not Built (Phases 5-6)

5. **JIVL Academy / JIVL Research** — see `docs/academy-roadmap.md` and
   `docs/research-roadmap.md`.
6. **JIVL Enterprise / JIVL Marketplace** — see
   `docs/enterprise-roadmap.md` and `docs/marketplace-roadmap.md`.

## Longer-Term, Unscheduled Ideas (Not Committed)

- GraalVM native-image build of the CLI (documented as a roadmap item in
  `README.md`; not implemented in this MVP).
- Additional JVM framework detectors (Quarkus, Micronaut) via the
  extension-interface design already established for framework detection.
- A hosted attestation service, only after the governance, signing, and
  revocation groundwork in `docs/badge-and-attestation-model.md` is
  actually built — not before.

Nothing in this file is a commitment or a timeline; it is a statement of
current intent, revisited as the project actually grows.
