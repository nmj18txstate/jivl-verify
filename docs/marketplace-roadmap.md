# JIVL Marketplace Roadmap (Documentation Only — Not Built)

**Marketplace functionality does not exist in this MVP. No payment
functionality exists anywhere in this repository.** This document is a
design placeholder for a possible future direction, not a commitment.

## Envisioned Components

- **Original or properly licensed content only** — the same bar already
  enforced for this repository's own `samples/` (Apache-2.0, original,
  no commercial-book-derived material) would extend to any marketplace
  listing.
- **Publisher identity verification** — confirming who is actually
  publishing a listing, distinct from `jivl.yaml`'s self-declared
  `provenance.author` field, which is an attestation, not proof
  (`docs/provenance-policy.md`).
- **Provenance verification** — a marketplace context raises the stakes on
  provenance claims considerably beyond the MVP's current
  attestation-only model; this would need dedicated design work, not an
  assumption that today's manifest checks are sufficient.
- **Skill licensing / educational-pack licensing / research-pack
  licensing** — distinct licensing regimes per content type (ordinary
  skill vs. Academy learning pack vs. Research artifact).
- **Creator royalties** and **platform commission** — a revenue-split
  model, not designed here.
- **Payment-provider boundaries** — JIVL itself would not become a payment
  processor; it would integrate with one, with a clear boundary for what
  JIVL's own systems ever touch (ideally: never raw payment credentials).
- **Tax boundaries** — jurisdiction-dependent, out of scope for engineering
  design at this stage.
- **Refunds, disputes, copyright complaints, takedowns, moderation** — all
  need explicit process design before any transaction ever occurs.
- **Conflict-of-interest controls** and **fraud prevention** — extending
  `GOVERNANCE.md`'s conflict-of-interest policy into a context with real
  money.
- **Badge independence** — the single most important carry-over rule:
  marketplace ranking, pricing, or featured placement must never be able
  to alter what a badge reports (see `docs/badge-and-attestation-model.md`
  and the next section).

## Rule: A Favorable Verification Result Cannot Be Purchased

This is not a marketplace feature to design later — it is a constraint the
marketplace design must satisfy from day one, restated from
`GOVERNANCE.md`: no payment, commission, or marketplace ranking may
suppress, downgrade, or hide a FAIL finding, alter a badge's derived
result, or bypass the requirement that effectiveness claims require an
actual executed baseline-vs-with-skill comparison.

## Separation of Marketplace Revenue From Verification Decisions

If ever built, marketplace revenue operations (payments, royalties,
commission) and verification operations (rule execution, report
generation, badge derivation) would need to remain architecturally
separate — verification code must not import from, or be aware of, any
future marketplace/billing module. This mirrors the existing
`jivl-registry` must-not-depend-on-`jivl-cli` boundary discipline already
enforced by ArchUnit in this MVP (see `docs/architecture.md`), applied to
a future module boundary that does not exist yet.

## Explicitly Not Promised

No timeline, no pricing model, and no confirmation this will ever be built.
