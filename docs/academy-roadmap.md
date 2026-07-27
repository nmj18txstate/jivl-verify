# JIVL Academy Roadmap (Documentation Only — Not Built)

JIVL Academy is a possible future direction: verified, runnable Java/JVM
learning packs built on top of the same verification evidence JIVL already
produces. **Nothing in this section is implemented.** No Academy code,
schema, or UI exists in this MVP.

## Concept

A "learning pack" would be a skill-shaped bundle that is not just verified
(compiles, builds, JDK-compatible) but pedagogically structured: it teaches
a JVM concept through progressively harder, runnable exercises, using the
same JIVL evidence as its quality floor.

## Envisioned Components

- **Verified learning packs** — a learning pack must pass ordinary JIVL
  verification before it can be published as a learning pack; verification
  is necessary but not sufficient.
- **Runnable Java/JVM labs** — exercises that compile/build/test via the
  same `jivl-maven`/`jivl-gradle` machinery already used for verification.
- **Learning objectives** — explicit, checkable statements of what a
  learner should be able to do afterward.
- **Prerequisites** — explicit dependency on other packs or assumed prior
  knowledge, so a catalog can be sequenced.
- **Instructor metadata** — attribution, review status, and (if ever
  monetized — see `docs/marketplace-roadmap.md`) royalty routing.
- **Student metadata** — progress tracking scoped to the individual
  learner; no cross-learner data sharing without explicit consent design
  (privacy model to be defined in a future RFC, not assumed here).
- **Progressive exercises** — smaller, scaffolded tasks building toward a
  capstone.
- **Quizzes** — objective-answer checks, distinct from open-ended coding
  exercises.
- **Capstone projects** — a larger, JIVL-verified project demonstrating
  combined mastery.
- **Version-specific Java tracks** — e.g., a Java 21 baseline track and a
  clearly separated, explicitly labeled preview/experimental track (mirror
  of this repository's own Java 21 vs. Java 26 separation).
- **Feedback rubrics** — structured, criterion-based feedback rather than a
  single pass/fail signal, consistent with JIVL's "findings, not a score"
  philosophy (`docs/verification-model.md`).
- **Evaluation of teaching quality** — distinct from evaluation of *skill*
  effectiveness (`jivl-evaluations`); would need its own model, not
  reused wholesale.
- **Prevention of immediate answer disclosure** — exercises would need a
  mechanism to withhold reference solutions until a learner has attempted
  the exercise, to avoid an agent (or a learner) trivially reading the
  answer key.
- **Academic-integrity considerations** — if used in a classroom context,
  a policy for what counts as acceptable AI-agent assistance versus
  academic dishonesty would need to be explicit and configurable per
  institution, not hardcoded.
- **University pilot model** — a possible path is a small number of opt-in
  pilot courses providing structured feedback before any broader rollout;
  no pilot currently exists.
- **Accessibility requirements** — WCAG-aligned content and UI, consistent
  with the accessibility bar already set for `jivl-registry` in this MVP.
- **Licensing and provenance requirements** — learning-pack content would
  need the same `jivl.yaml`-style provenance discipline as any other skill,
  and explicit licensing suitable for educational reuse (not assumed to be
  the same license as ordinary skills without a deliberate decision).

## Explicitly Not Promised

No timeline, no funding model, and no confirmation this will ever be
built. This section exists so that if it *is* built later, it starts from
a considered design rather than an ad hoc one.
