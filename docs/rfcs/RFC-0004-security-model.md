# RFC-0004: Security Model

- **Author:** Nityam Jigyasu (@nmj18txstate)
- **Status:** Accepted
- **Created:** 2026-07-27
- **Last updated:** 2026-07-27

## Summary

Define the static security-rule model in `jivl-security`: rule
identifiers, evidence/remediation requirements, and the explicit limits of
what static scanning can claim.

## Motivation

Skills can embed secrets, destructive commands, or exfiltration/obfuscation
patterns. A deterministic, testable, best-effort static scanner catches a
meaningful subset of these cheaply and safely (it never executes anything
it scans), but must be honest about not being a complete safety proof.

## Goals

- Stable rule IDs (`JIVL-SEC-001`, `JIVL-SEC-002`, ...) covering: embedded
  API keys/tokens, private key material, password assignment, credential-
  file/SSH-key/cloud-credential-directory access, destructive filesystem
  commands, download-then-execute and pipe-to-shell patterns, encoded-
  payload decode-and-execute, PowerShell encoded commands, environment-
  variable enumeration, broad home-directory traversal, shell-history and
  browser-credential-store access, security-control/AV/firewall/TLS-
  verification disabling, unrestricted `allowed-tools` declarations,
  hidden bidirectional Unicode controls, suspicious unexplained executable
  scripts, command substitution in network commands, writes targeting
  agent-configuration directories, permission-bypass instructions, and
  exfiltration instructions.
- Every rule: explains why it matched, provides minimally-quoted redacted
  evidence, provides remediation, and has both a positive and a negative
  fixture test.
- Never execute a detected command, never follow a URL found in the skill,
  never fetch remote content while scanning.

## Non-Goals

- Not a claim that passing static checks proves complete safety (see
  `docs/threat-model.md`, "Non-Threats / Explicit Non-Goals").
- Not a dynamic/sandboxed execution-based scanner in this MVP.

## Design

See `docs/threat-model.md` for full threat coverage and reasoning; this
RFC is the policy-level commitment that `jivl-security` rules stay
deterministic, evidence-based, and testable, and that new rules go through
the RFC process per `GOVERNANCE.md`'s "Rules for Changing Verification
Policy" since severity/rule changes affect verification policy directly.

## Security Considerations

This entire RFC *is* a security consideration; notably, evidence
redaction must be conservative enough that a finding's own "evidence"
field cannot itself become a secret-leak vector.

## Compatibility Considerations

New rules are additive to existing reports (new `ruleId`s appearing over
time); removing or changing the meaning of an existing rule ID is treated
as a breaking verification-policy change requiring its own RFC.

## Alternatives

- **LLM-as-judge content scoring** (as used by `skill-validator` for
  quality dimensions — see `docs/competitive-landscape.md`). Considered
  and explicitly rejected for `jivl-security`: JIVL's security rules stay
  deterministic and reproducible rather than model-judgment-based, so a
  given input always produces the same finding.

## Open Questions

- Exact false-positive-reduction tuning per rule; deferred to Phase 1
  implementation with real fixture-driven iteration, not decided
  abstractly here.

## Decision History

- 2026-07-27: Accepted; implementation deferred to Phase 1.
