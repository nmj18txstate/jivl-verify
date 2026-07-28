# Governance

JIVL is a young, single-maintainer open-source project. This document
describes how it is governed today and how governance is expected to evolve.

## Roles

### Creator and Lead Maintainer
Nityam Jigyasu (@nmj18txstate) created JIVL and currently holds final
decision authority over the project: architecture direction, verification
policy, release timing, and RFC acceptance. This role also carries
responsibility for security response (see SECURITY.md) and Code of Conduct
enforcement.

### Contributor
Anyone who opens an issue, submits a pull request, writes a sample skill,
or participates in an RFC discussion. No special access is required.

### Reviewer
A contributor who has been asked by the Lead Maintainer to review pull
requests in a specific area (e.g., "Reviewer: jivl-security"). Reviewers may
approve changes but merges in this MVP phase remain gated by the Lead
Maintainer.

### Maintainer
A Reviewer with sustained, high-quality contribution history who is granted
merge rights over one or more modules by the Lead Maintainer. There are no
Maintainers other than the Lead Maintainer as of this writing.

## Decision-Making Process

1. Small, uncontroversial changes (bug fixes, test additions, docs fixes) go
   through ordinary pull-request review.
2. Changes that affect verification policy, finding severities, badge
   semantics, module boundaries, or public API/CLI surface must go through
   the RFC process (`docs/rfcs/README.md`) before implementation.
3. In case of disagreement during the MVP phase, the Lead Maintainer makes
   the final call and records the reasoning in the RFC's "Decision History"
   section.
4. As the contributor base grows, decision authority for specific modules
   may be delegated to Maintainers; this document will be updated when that
   happens, not silently assumed.

## RFC Lifecycle

RFCs move through: `Draft` -> `Proposed` -> `Accepted` -> `Implemented` |
`Superseded` | `Rejected`. See `docs/rfcs/README.md` for the full template
and current RFC index.

## Security Disclosure Process

Security issues are handled per `SECURITY.md` and are never discussed in
public issues before a fix or mitigation is available.

## Conflict of Interest Policy

- Maintainers and Reviewers must disclose any financial or employment
  relationship with a submitter when reviewing that submitter's skill,
  sample, or verification-policy change.
- A Maintainer with a disclosed conflict of interest must not be the sole
  approver of a change that benefits them or their employer.
- This applies with particular force to anything that could affect a
  verification *outcome* — see the next section.

## Rules Preventing Payment for Favorable Verification Outcomes

These rules are foundational and are not subject to ordinary RFC override
without an explicit, public governance RFC and a defined transition period:

1. No payment, sponsorship, or in-kind benefit may cause a FAIL finding to
   be suppressed, downgraded, or hidden from a generated report.
2. No payment may cause a badge to render a result other than what the
   underlying report data supports (see `docs/badge-and-attestation-model.md`).
3. Verification rule logic (jivl-security, structural validation, JDK/
   framework compatibility) must remain open source and auditable; a rule
   change that benefits a specific paying party must go through the same
   public RFC process as any other rule change.
4. Any future commercial offering (see `docs/enterprise-roadmap.md`,
   `docs/marketplace-roadmap.md`) may charge for infrastructure, private
   hosting, compute, retention, or support — never for a specific
   verification verdict.

## Future Community Governance Path

As JIVL gains contributors, the intended path is:
1. Lead Maintainer designates area Reviewers.
2. Reviewers with sustained contributions become Maintainers with scoped
   merge rights.
3. A steering group of Maintainers is established for cross-cutting
   verification-policy RFCs, with the Lead Maintainer retaining a tie-break
   vote until the project reaches a defined contributor/adoption threshold
   (to be defined in a future governance RFC).
No timeline is promised for these transitions; they are triggered by actual
project growth, not a calendar date.

## Rules for Changing Verification Policy

A change to *what JIVL checks* or *how it scores/labels results* (new rule,
changed severity, new badge state, new finding category) requires:
1. An RFC under `docs/rfcs/` describing motivation, the exact rule/behavior
   change, and backward-compatibility impact on existing reports/badges.
2. At least one positive and one negative test fixture demonstrating the
   new behavior before merge.
3. An entry in `CHANGELOG.md` and, if the schema changes, a `schemaVersion`
   bump documented in the relevant `schemas/*.json` file.
