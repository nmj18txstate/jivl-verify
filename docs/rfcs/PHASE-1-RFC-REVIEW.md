# Phase 1 RFC Acceptance Readiness Review

- **Prepared:** 2026-07-28, as part of the Phase 1 kickoff/governance-review
  task on branch `feat/phase-1-core-contracts`.
- **Purpose:** Assess whether RFC-0001 and RFC-0002 are ready for the Lead
  Maintainer (Nityam Jigyasu, @nmj18txstate) to move to `Accepted`, per the
  lifecycle in `docs/rfcs/README.md`. This document is **not itself an
  RFC** and does not change either RFC's status — RFC-0001 and RFC-0002
  remain `Draft` after this review. It exists to give the Lead Maintainer
  everything needed to make that decision without re-deriving it.
- **Scope:** RFC-0001 (JIVL Vision) and RFC-0002 (Verification Report
  Model) only, because these are the two RFCs Phase 1A's implementation
  most directly depends on. RFC-0003 through RFC-0007 are out of scope for
  this review (they gate later Phase 1 sub-phases or later phases
  entirely) and remain untouched.

## Summary Recommendation

| RFC | Recommendation |
|---|---|
| RFC-0001 (JIVL Vision) | **Accept with listed amendments** (see below) |
| RFC-0002 (Verification Report Model) | **Accept with listed amendments** — accept the `Finding`-model portion needed for Phase 1A now; keep the full report-envelope portion (JSON top-level fields, `reportDigest`, `schemaVersion`, environment summary) open pending the unresolved question below and Phase 2 timing |

**Bottom line on the question this review was specifically asked to
answer:** yes, Phase 1A can and should proceed on a narrow contract —
`FindingStatus`, `FindingSeverity`, `FindingCategory`, `RuleId`, the safe
relative-path and redacted-evidence value types, the immutable `Finding`
record, deterministic ordering, `VerificationSummary`, and the
`VerificationRule`/`VerificationContext` contracts — **without** first
freezing the full JSON report envelope, digest computation, environment
summary field list, badge model, CLI, or registry. Those are real,
necessary future work, but locking them in now would front-load
irreversible decisions (especially the environment-summary question below)
before Phase 1A/1B/1C implementation experience exists to inform them.

---

## 1. RFC-0001: JIVL Vision

### 1. RFC reviewed
`docs/rfcs/RFC-0001-jivl-vision.md` — Status: `Draft`, Created/Last
updated: 2026-07-27 / 2026-07-28.

### 2. Proposed implementation scope
RFC-0001 is a founding-thesis document, not a technical contract. It
commits the project to: producing categorized, evidence-backed findings
(not a single score); making the DECLARED/DETECTED/EXECUTED/VERIFIED
distinction first-class; keeping verification rule logic open source and
auditable; and never letting static checks claim to prove complete safety
or letting content-quality substitute for an executed effectiveness
evaluation. It does not itself specify any API, data shape, or module
boundary — those are RFC-0002 through RFC-0007's job.

### 3. Decisions already clear
- The project is JVM-specific, not a general-purpose skill validator
  (explicit Non-Goal, consistent with `docs/competitive-landscape.md`).
- Findings, not a single score (consistent with ADR-0003 — already
  Accepted as an ADR, so RFC-0001 restates rather than introduces this).
- The DECLARED/DETECTED/EXECUTED/VERIFIED/UNVERIFIED/CONTRADICTORY model
  is foundational (consistent with `docs/verification-model.md`, which is
  already written in these terms).
- No hosted attestation, marketplace, or payment functionality in the MVP
  (consistent with `README.md`'s "Explicit Non-Goals").

### 4. Open questions
Both listed in RFC-0001 itself are genuinely non-blocking for Phase 1A:
- Whether a GraalVM native-image distribution is worth the complexity —
  irrelevant until there is a CLI to distribute (Phase 1D+).
- Whether additional JVM frameworks (Quarkus, Micronaut) should be added
  before or after the first tagged release — irrelevant until
  `jivl-maven`/`jivl-gradle` framework detectors exist (RFC-0003's scope,
  not Phase 1A).

Neither open question blocks accepting RFC-0001's founding thesis or
starting Phase 1A.

### 5. Contradictions with other repository documents
None found. RFC-0001's goals are consistent with ADR-0001 (module
boundaries), ADR-0003 (no single score), `docs/verification-model.md`, and
`docs/threat-model.md`. One **soft risk**, not a contradiction: RFC-0001
describes the *entire* MVP vision (CLI, registry, evaluations, JVMskills.com
integration, etc.) in one document. Accepting it as-is could be read as
blanket authorization for all of Phases 1-4 at once, which is not the
intended incremental process — `docs/implementation-plan.md`'s phase gates
and the per-module RFCs (0002-0007) are what actually gate each phase.
This is addressed in the proposed amendment below, not by withholding
acceptance.

### 6. Security implications
None beyond what's already covered by `docs/threat-model.md`, which
RFC-0001 references rather than restates. Accepting RFC-0001 does not by
itself authorize any new execution surface.

### 7. Compatibility implications
None — RFC-0001 predates any released artifact or schema; there is
nothing yet to break compatibility with.

### 8. Items that should be deferred
GraalVM native-image and additional-framework-timing questions (see Open
Questions above) — explicitly already deferred within RFC-0001 itself, no
change needed.

### 9. Recommendation
**Accept with listed amendments.** The vision itself is sound, consistent
with already-Accepted ADRs, and does not encode any premature technical
commitment. The amendment is about scope-of-authorization clarity, not
content correction.

### 10. Exact proposed amendments
Add one sentence to RFC-0001's Decision History (not the Summary/Goals —
the thesis itself needs no wording change) at acceptance time:

> Accepting this RFC authorizes continued Phase 1 planning and the
> founding thesis it describes; it does not by itself authorize
> implementation of any specific module's design. Each module's detailed
> design (RFC-0002 through RFC-0007) must independently reach `Accepted`
> status, per `docs/rfcs/README.md`, before that module's implementation
> begins.

### 11. Exact human approval text the Lead Maintainer could use
In `RFC-0001-jivl-vision.md`:

```
- **Status:** Accepted
```

Decision History addition:

```
- 2026-XX-XX: Accepted by Nityam Jigyasu (@nmj18txstate), Lead Maintainer,
  as the founding thesis for the MVP scope described in
  `docs/implementation-plan.md`. This acceptance authorizes continued
  Phase 1 planning and the thesis itself; it does not authorize
  implementation of any specific module's design — RFC-0002 through
  RFC-0007 must each independently reach `Accepted` before their
  respective module's implementation begins.
```

(`docs/rfcs/README.md`'s Current RFC Index row for RFC-0001 should be
updated from `Draft` to `Accepted` in the same change.)

---

## 2. RFC-0002: Verification Report Model

### 1. RFC reviewed
`docs/rfcs/RFC-0002-verification-report-model.md` — Status: `Draft`,
Created/Last updated: 2026-07-27 / 2026-07-28.

### 2. Proposed implementation scope
RFC-0002 bundles two distinguishable concerns into one design:
1. **The `Finding` domain model**: `Finding` fields (`ruleId`, `category`,
   `severity`, `status`, `title`, `explanation`, `evidence`, `file`,
   `line`, `remediation`, `verifierVersion`), the `FindingStatus` set
   (PASS/WARN/FAIL/SKIPPED/ERROR), `FindingSeverity` set (INFO..CRITICAL),
   and `FindingCategory` set. This is exactly Phase 1A's scope.
2. **The full report envelope**: JSON top-level fields (`schemaVersion`,
   `jivlVersion`, `reportId`, `reportDigest`, `startTime`,
   `completionTime`, target skill identity, `commitSha`, environment
   summary, `findings[]`, per-tool evidence blocks, aggregate counts,
   `attestationType`), plus the digest computation and its excluded-field
   list. This is Phase 2 (`jivl-reporting`) scope — it doesn't exist as
   code anywhere yet and nothing in Phase 1A-1C needs it.

### 3. Decisions already clear (concern 1 — Finding model)
- `Finding` field list and its five statuses/five severities/fourteen
  categories are fully specified and consistent with
  `docs/verification-model.md`, which already documents the same taxonomy
  — this is not a new decision RFC-0002 is introducing, just formalizing
  it as an RFC.
- `Clock` injection everywhere a timestamp is produced — consistent with
  `AGENTS.md`'s Java style rules, directly actionable for
  `VerificationContext` in Phase 1A.
- No single aggregate score — consistent with ADR-0003.

### 4. Decisions already clear (concern 2 — report envelope)
- Digest is SHA-256 over canonical content, excluding fields that vary
  run-to-run without the underlying skill changing (`startTime`,
  `completionTime`, environment fields) — the *principle* is clear even
  though the *exact* excluded-field list is not (see Open Questions).
- `schemaVersion` bump discipline ties to `GOVERNANCE.md`'s "Rules for
  Changing Verification Policy" — already decided policy, RFC-0002 just
  applies it here.

### 5. Open questions — **do not silently decide these**
RFC-0002's own "Open Questions" section states:

> Exact list of "environment summary" fields to include without leaking
> local absolute paths (must satisfy the registry's "never expose local
> paths" requirement even when reports are generated locally).

This is explicitly unresolved in the RFC as written, and this review does
**not** propose a resolution. Flagging why it matters and why it should
stay open rather than be quietly decided during Phase 1A implementation:

- It is a genuine security-relevant decision (`docs/threat-model.md`'s
  "Registry-Specific Threats": "a report field containing a local absolute
  filesystem path... could leak host information"). Deciding it as a
  side-effect of writing `jivl-core` code, without the Lead Maintainer
  weighing in, would be exactly the kind of "silently decided" outcome
  this review was asked to avoid.
- It has no bearing on Phase 1A. `VerificationContext`, `Finding`, and the
  rule-execution contracts do not need an environment-summary shape to
  exist. It only matters once `jivl-reporting` (Phase 2) actually
  serializes a report.
- Resolving it prematurely risks freezing a field list before Phase
  1B/1C's actual implementation reveals what environment data is genuinely
  useful to report versus merely convenient to log locally.

**This question is left open for Nityam's decision, to be resolved no
earlier than Phase 2 kickoff, and explicitly not as part of accepting the
Finding-model portion of RFC-0002 now.**

Additionally open, also non-blocking for Phase 1A:
- Non-Goals note flags ambiguity about whether the badge model is fully
  separate from RFC-0002 or partially covered by it ("actually see the
  dedicated `docs/badge-and-attestation-model.md` and ADR-0005" — this is
  a documentation cross-reference cleanup, not a design gap, but the Lead
  Maintainer may want to tidy the wording when accepting).

### 6. Contradictions with other repository documents
None found for the Finding-model portion. For the report-envelope
portion: no contradiction, but a **sequencing risk** — accepting RFC-0002
wholesale right now would commit to a JSON shape and digest algorithm
before any code exists to validate that shape is actually implementable
and sufficient, and before the environment-summary question (which the
RFC itself defers) is settled. `docs/implementation-plan.md`'s Phase 1A
scope (added this pass) deliberately excludes the report envelope for
exactly this reason — accepting RFC-0002 as a single unit would create a
mismatch between "RFC says Accepted, therefore implement all of it" and
"the phase plan says the envelope is Phase 2." The proposed amendment
below resolves this by scoping the acceptance, not by changing the RFC's
technical content.

### 7. Security implications
- The redacted-evidence requirement (`Finding.evidence` must not itself
  leak a secret) is squarely Phase 1A scope and should be accepted now —
  it is listed as a Phase 1A deliverable (the "redacted evidence value
  type") in `docs/implementation-plan.md`.
- The environment-summary/local-path-leak question (above) is squarely
  Phase 2 scope and should not be decided now.

### 8. Compatibility implications
`schemaVersion` bump discipline (tied to `GOVERNANCE.md`) only applies
once a `schemaVersion` exists to bump — i.e., Phase 2. No compatibility
concern for Phase 1A, which produces no serialized artifact.

### 9. Items that should be deferred
- Full JSON report top-level field list.
- `reportDigest` computation and its exact excluded-field list.
- Environment summary field list (explicitly — see Open Questions).
- Everything downstream of the report shape: badge model, CLI report
  rendering, registry ingestion/validation.

### 10. Exact proposed amendments
Add a scope-splitting note to RFC-0002's Decision History at acceptance
time (the Design section's technical content does not need to change —
only the acceptance scope needs to be made explicit):

> Accepted for the Finding domain model only: `FindingStatus`,
> `FindingSeverity`, `FindingCategory`, and the `Finding` record fields
> (`ruleId`, `category`, `severity`, `status`, `title`, `explanation`,
> `evidence`, `file`, `line`, `remediation`, `verifierVersion`), as needed
> for Phase 1A (`jivl-core`). The JSON report envelope (`schemaVersion`,
> `reportId`, `reportDigest` and its excluded-field list, environment
> summary, and other top-level report fields) remains open pending a
> future amendment to this RFC, to be revisited no earlier than Phase 2
> kickoff. In particular, the "environment summary" field list open
> question is explicitly not resolved by this acceptance and requires its
> own Lead Maintainer decision before `jivl-reporting` implementation
> begins.

### 11. Exact human approval text the Lead Maintainer could use
In `RFC-0002-verification-report-model.md`:

```
- **Status:** Accepted
```

Decision History addition:

```
- 2026-XX-XX: Accepted by Nityam Jigyasu (@nmj18txstate), Lead Maintainer,
  for the Finding domain model only (FindingStatus, FindingSeverity,
  FindingCategory, and the Finding record fields) as needed for Phase 1A
  (jivl-core). The JSON report envelope (schemaVersion, reportId,
  reportDigest and its excluded-field list, environment summary, other
  top-level report fields) remains open, to be revisited via a future
  amendment no earlier than Phase 2 kickoff. The "environment summary"
  field list question in particular remains explicitly unresolved and
  requires its own decision before jivl-reporting implementation begins.
```

(`docs/rfcs/README.md`'s Current RFC Index row for RFC-0002 should be
updated from `Draft` to `Accepted` in the same change, with an asterisk or
note if the Lead Maintainer wants the scoped-acceptance caveat visible
there too.)

---

## Cross-Cutting Note for Both RFCs

If the Lead Maintainer prefers a cleaner mechanism than a scoped
"Accepted (partial)" status — since the RFC lifecycle in
`docs/rfcs/README.md` does not currently define a partial-acceptance
state — an alternative is to keep RFC-0002 `Draft` a little longer and
instead accept only a new, narrower RFC (e.g. RFC-0008) that extracts
just the Finding-model portion, leaving RFC-0002 to cover the report
envelope alone once environment-summary is resolved. This review does not
recommend that path over the amendment approach above (it adds
process overhead for a small, low-risk contract), but records it as a
genuine alternative for the Lead Maintainer to choose, consistent with
this review's instruction not to silently decide the underlying question.
