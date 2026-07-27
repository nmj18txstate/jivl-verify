# JIVL Enterprise Roadmap (Documentation Only — Not Built)

**Enterprise tenancy does not exist in this MVP.** This document describes
a possible future commercial direction so that, if built, it starts from a
considered design.

## Envisioned Components

- **Private report registries** — an org-scoped deployment of
  `jivl-registry` (or a hosted equivalent) holding reports an org does not
  want public.
- **Private Agent Skills** — verification of skills living in private
  repositories, without the report itself becoming public.
- **Organization tenancy** — data isolation between orgs sharing
  infrastructure, if ever hosted rather than self-run.
- **RBAC** — role-based access to reports, policy configuration, and
  audit views, distinct from JIVL's current open-source project
  Contributor/Reviewer/Maintainer roles (`GOVERNANCE.md`), which govern the
  *project*, not a tenant's private deployment.
- **Policy-as-code** — an org-defined policy (e.g., "FAIL the pipeline on
  any HIGH-severity security finding") expressed declaratively rather than
  hardcoded into CI scripts.
- **Approval workflows** — human sign-off steps layered on top of, not
  replacing, JIVL's automated findings.
- **Audit events** — who ran what verification, when, against what commit.
- **Signed attestations** — cryptographic signing of reports/badges,
  building on the not-yet-built hosted-attestation groundwork in
  `docs/badge-and-attestation-model.md`.
- **Commit binding** — a signed attestation must be bound to an exact
  commit SHA to be meaningful; `--commit-sha` already exists as a CLI
  input in the MVP as groundwork for this.
- **Retention controls** — how long an org's reports/attestations are kept
  and under what deletion policy.
- **Private repository verification** — running JIVL against a repository
  the operator has access to without exposing that access more broadly.
- **Self-hosted verification workers** — letting an org run the JIVL
  execution/build step entirely within its own network boundary.
- **Network isolation** — a defined boundary for what a self-hosted worker
  is and is not allowed to reach, especially relevant given
  `--allow-build-network`.
- **Secret-management boundaries** — explicit rules for what an enterprise
  deployment must never log or expose (extending `SECURITY.md`'s existing
  redaction requirements to a multi-tenant context).
- **Compliance evidence** — exportable evidence suitable for an org's own
  compliance/audit processes.
- **Skill version pinning** — verifying and referencing an exact skill
  version, not just "the latest".
- **Revocation** — a way to revoke a previously issued attestation if a
  problem is discovered later.
- **Deprecation** — a lifecycle state for skills/attestations that are no
  longer recommended, distinct from outright revocation.
- **IDE and coding-agent integration** — surfacing JIVL evidence directly
  inside a developer's IDE or coding agent, not just in CI/registry output.
- **Usage analytics with privacy controls** — if ever built, would need an
  explicit, documented privacy model; JIVL currently has zero external
  analytics anywhere (`AGENTS.md`), and that stays true for this MVP.
- **Support and service-level boundaries** — a paid tier could reasonably
  include support/SLA commitments; it must never include a more favorable
  verification *outcome* (see `GOVERNANCE.md`).

## Explicitly Not Promised

No timeline, no pricing, no confirmation this will ever be built. Payment
functionality does not exist anywhere in this repository today.
