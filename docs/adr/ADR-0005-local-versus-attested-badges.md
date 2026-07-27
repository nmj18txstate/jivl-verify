# ADR-0005: Badges Are Always Derived, Never Hand-Set, and Distinguish Trust Levels

- **Status:** Accepted
- **Date:** 2026-07-27

## Context

A badge is only useful if its meaning is trustworthy. Two failure modes
would undermine that: (a) badge status becoming a settable value
independent of the underlying report, and (b) a self-run local badge being
visually indistinguishable from a stronger, independently-produced
attestation that does not yet exist.

## Decision

- Badge generation is a pure function of a report's findings; there is no
  CLI flag, config key, or API to set `result` directly.
- Only two attestation types exist in the MVP: `LOCAL_SELF_VERIFICATION`
  and `CI_SELF_VERIFICATION`. `JIVL_HOSTED_ATTESTATION` is defined in the
  schema as a future value but is never produced or displayed by this MVP.
- Every rendering of a badge (SVG, Markdown, registry UI) must visibly
  distinguish "self-verified through JIVL CLI" from "verified through
  project CI".

## Alternatives Considered

- **A single generic "Verified" badge with no attestation-type
  distinction.** Rejected: it would let a local, unaudited self-run look
  identical to a CI-produced one, which is a meaningfully different trust
  level once a hosted attestation service (or even just "ran in someone
  else's CI") enters the picture.

## Consequences

- Slightly more badge-rendering complexity (two labeled variants instead
  of one) in exchange for not overstating trust level.
- Building a real `JIVL_HOSTED_ATTESTATION` later requires satisfying the
  prerequisites listed in `docs/badge-and-attestation-model.md` (signing,
  commit binding, revocation, etc.) before that enum value is ever
  actually emitted.
