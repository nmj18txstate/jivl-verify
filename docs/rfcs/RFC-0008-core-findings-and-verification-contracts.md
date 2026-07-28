# RFC-0008: Core Findings and Verification Contracts

- **Author:** Nityam Jigyasu (@nmj18txstate)
- **Status:** Accepted
- **Created:** 2026-07-28
- **Last updated:** 2026-07-28

Approved by Nityam Jigyasu, Lead Maintainer, in this session (2026-07-28),
extracted from RFC-0002's Finding-domain portion per the governance
decision recorded in that RFC's Decision History and in
`docs/rfcs/PHASE-1-RFC-REVIEW.md` (§2).

## Summary

Define the Phase 1A contract for `jivl-core`: the `Finding` domain model
(`FindingStatus`, `FindingSeverity`, `FindingCategory`, `RuleId`,
`SafeRelativePath`, `RedactedEvidence`, `Finding` itself), deterministic
finding ordering, `VerificationSummary` aggregation, and the
`VerificationRule`/`VerificationContext` execution contracts. This RFC
covers exactly what Phase 1A implements — nothing about report
serialization, JSON schemas, or anything downstream of an in-memory
`Finding`.

## Motivation

RFC-0002 originally bundled the Finding domain model together with the
full JSON report envelope (`schemaVersion`, `reportId`, `reportDigest`,
environment summary, and other top-level report fields). The Lead
Maintainer's governance review (`docs/rfcs/PHASE-1-RFC-REVIEW.md`, §2)
found that bundling forced two unrelated decisions to be accepted or
rejected together: a narrow, low-risk, immediately-implementable domain
model, and a broad, only-partially-specified report envelope whose own
"environment summary" field list is explicitly unresolved. Extracting the
domain-model portion into its own RFC lets Phase 1A (`jivl-core`)
implementation begin without prematurely freezing the report envelope,
while RFC-0002 stays `Draft` and open for that larger, still-unresolved
design.

## Goals

- Give every rule-producing module (`jivl-security`, `jivl-maven`,
  `jivl-gradle`, and later modules) one shared, stable `Finding` model and
  a `VerificationRule` contract to implement against.
- Make `Finding` immutable, safe to hold arbitrarily long (no absolute
  local paths, no raw secret values), and deterministically ordered so
  that repeated runs over unchanged input produce byte-identical ordering.
- Provide `VerificationContext` as the one place a `Clock` and other
  cross-cutting, non-secret execution context is injected, per
  `AGENTS.md`'s "Clock injection... never `Instant.now()` directly."
- Keep `jivl-core` completely framework-free (no Spring, Picocli, Jackson,
  Maven, Gradle, or logging-framework types), per `AGENTS.md` and
  ADR-0001.
- Aggregate counts via `VerificationSummary` without ever collapsing
  findings into a single opaque score, per ADR-0003.

## Non-Goals

This RFC explicitly does **not** cover, and no Phase 1A implementation
under this RFC may include:

- `SKILL.md` parsing or any frontmatter/YAML parsing (Phase 1B).
- Security-rule implementations (`jivl-security`, later phases).
- Provenance-rule implementations (`docs/provenance-policy.md`, later
  phases).
- JSON serialization of any kind.
- The JSON report envelope: `schemaVersion`, `reportId`, `reportDigest`
  (including its excluded-field list), or any other RFC-0002 top-level
  report field.
- The environment-summary schema (remains RFC-0002's open question,
  explicitly not resolved here or by extension).
- Badges or any badge/attestation model.
- CLI commands, flags, or exit codes.
- The Spring Boot registry.
- Maven or Gradle child-process execution.
- Java compilation execution.
- Agent-effectiveness evaluation.
- Hosted attestations.

Any of the above requires its own RFC (RFC-0002's remaining scope, or a
new RFC) reaching `Accepted` before implementation.

## Design

All types below live in `jivl-core` only, as immutable Java records/enums,
per `AGENTS.md`'s Java style rules (constructor injection, no field
injection, no wildcard imports, no static mutable global state, no
Lombok, no Java preview features, no `System.out`).

### 1. `FindingStatus`

Enum: `PASS`, `WARN`, `FAIL`, `SKIPPED`, `ERROR` — exactly the set
documented in `docs/verification-model.md`. No implicit ordering is
assumed from declaration order for anything status-critical; the
deterministic `Finding` ordering below defines its own explicit severity
order rather than relying on enum declaration order for severity.

### 2. `FindingSeverity`

Enum: `INFO`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL` — exactly the set
documented in `docs/verification-model.md`.

**Chosen and documented ordering** (least to most severe), used both for
any severity comparison and as input to `Finding`'s deterministic
ordering below:

```
INFO < LOW < MEDIUM < HIGH < CRITICAL
```

This matches the enum's natural declaration order, so `Enum.compareTo`
(ordinal order) is the deterministic comparator — declared explicitly
here so a future reordering of the enum's declaration is understood to be
a breaking change to this documented ordering, not a free refactor.

### 3. `FindingCategory`

Enum, exactly the fourteen values documented in
`docs/verification-model.md`: `STRUCTURE`, `CONTENT`, `REFERENCE`,
`SECURITY`, `PROVENANCE`, `LICENSE`, `JAVA`, `MAVEN`, `GRADLE`,
`JDK_COMPATIBILITY`, `FRAMEWORK_COMPATIBILITY`, `BUILD`, `EVALUATION`,
`REPORTING`. Ordering for `Finding` ordering purposes (below) is
**alphabetical by category name**, not declaration order — this is
documented explicitly so category ordering does not silently change if
the enum's declaration order is edited for readability later.

### 4. `RuleId`

Immutable value type (record wrapping a `String`) representing a stable
rule identifier such as `JIVL-STRUCT-001`.

- Non-null, non-blank (constructor-enforced; blank/null throws
  `IllegalArgumentException` immediately, not a validation `Finding` — a
  `RuleId` that fails to construct is a programming error in the rule
  module, not skill input).
- Canonical format: an uppercase, hyphen-separated identifier matching a
  documented pattern (module-prefix, category-abbreviation, zero-padded
  sequence number — e.g. `JIVL-STRUCT-001`, `JIVL-SEC-014`). The exact
  regular expression is an implementation detail enforced by the
  constructor, not repeated field-by-field here, but it must reject
  whitespace, lowercase letters, and empty segments.
- Deterministic equality and ordering: value-based `equals`/`hashCode`
  (record default), and `Comparable<RuleId>` by natural string ordering
  of the canonical form.

### 5. `SafeRelativePath`

Immutable value type (record wrapping a normalized relative path string
or equivalent internal representation).

- Accepts only normalized **relative** paths.
- Rejects absolute paths (constructor-enforced).
- Rejects path traversal (`..` segments, encoded traversal sequences) —
  ties directly to `docs/threat-model.md`'s "A Skill Tries to Escape Its
  Own Directory" mitigation.
- Rejects blank paths.
- Never exposes a host-specific absolute filesystem path through any
  accessor — only the normalized relative form is retrievable. This is
  what lets `Finding.file` be safe to hold and, later, safe to report,
  without a separate redaction step at report time.

### 6. `RedactedEvidence`

Immutable value type (record wrapping a `String`) representing evidence
text that is **already** safe to include in output.

- Non-null (blank is permitted — "no evidence text" is a valid state,
  represented as an empty string, not `null`).
- **Constructing this type does not itself perform secret scanning or
  redaction.** This is documented explicitly and repeatedly at the type's
  declaration site: `RedactedEvidence` is a marker that the *caller*
  attests the text has already been redacted (per
  `docs/threat-model.md`'s "Secrets Leak Through JIVL's Own Output"); it
  is the calling rule module's responsibility (e.g. `jivl-security`'s
  future redaction logic) to redact before constructing one. This RFC
  does not implement redaction logic — that is out of scope, deferred to
  whichever module first needs it (most likely `jivl-security`, a later
  phase).
- No API on this type re-exposes a mutable backing structure.

### 7. `Finding`

Immutable record with fields:

```
RuleId ruleId
FindingCategory category
FindingSeverity severity
FindingStatus status
String title
String explanation
RedactedEvidence evidence
Optional<SafeRelativePath> file
Optional<Integer> line   // constructor-enforced positive when present
String remediation
String verifierVersion
```

Requirements:

- Immutable: all fields final, no setters, no mutable collection fields
  on `Finding` itself (it does not hold a collection).
- Constructor invariants: `ruleId`, `category`, `severity`, `status`,
  `title`, `explanation`, `evidence`, `remediation`, and `verifierVersion`
  are non-null; `title`, `explanation`, `remediation`, and
  `verifierVersion` are non-blank; `line`, when present, is a positive
  integer (rejects zero and negative values — line numbers are 1-based);
  `file` and `line` are independently optional (a `Finding` may reference
  a file without a line, but not a line without a file — constructor
  rejects `line` present with `file` absent).
- No absolute file paths: enforced transitively by requiring `file` to be
  a `SafeRelativePath` (item 5) rather than a raw `String` or
  `java.nio.file.Path`.
- No raw secret-value fields: enforced by requiring `evidence` to be a
  `RedactedEvidence` (item 6) rather than a raw `String`.
- Deterministic comparison order: `Finding implements Comparable<Finding>`
  (or an equivalent documented `Comparator<Finding>` supplied alongside
  it), ordering by, in order:
  1. `category` (alphabetical by category name, per item 3)
  2. `severity` (`CRITICAL` first — i.e. **descending** severity, most
     severe first — using the `INFO < LOW < MEDIUM < HIGH < CRITICAL`
     ordering from item 2, reversed for display-first-things-first
     purposes)
  3. `ruleId` (ascending, per item 4's natural ordering)
  4. `file` (present-before-absent; among present values, ascending by
     the normalized relative path string)
  5. `line` (present-before-absent; among present values, ascending
     numeric)
  6. `title` (ascending, ordinal string comparison)

  This exact order is chosen so that findings naturally group by category,
  surface the most severe issue in each category first, then break ties
  deterministically all the way down to `title`, guaranteeing a total
  order with no two distinct `Finding` instances comparing as equal
  unless they are field-for-field identical.

### 8. `VerificationSummary`

Immutable record aggregating `Finding` counts by status:

```
int passCount
int warnCount
int failCount
int skippedCount
int errorCount
```

- Constructed by aggregating a `List<Finding>` (or equivalent immutable
  collection, see item 9) — a static factory (e.g.
  `VerificationSummary.from(findings)`) rather than a public mutable
  builder, keeping construction deterministic and side-effect-free.
- Deterministic: aggregating the same finding list twice produces an
  identical `VerificationSummary`.
- No opaque numeric quality score is derived or exposed anywhere on this
  type — only the five per-status counts, per ADR-0003.

### 9. `VerificationRule`

A framework-free functional contract:

```
Finding-producing contract: given a VerificationContext, returns
List<Finding>
```

This RFC chooses **`List<Finding>`** (immutable, e.g.
`List.copyOf(...)`-backed) as the explicit return contract, over a
custom immutable collection type — `List<Finding>` is already part of the
JDK, requires no additional type for callers to learn, and immutability is
enforced at the point of construction (`List.copyOf`) rather than needing
a bespoke collection class. A rule implementation must not require
Spring, Picocli, Jackson, Maven, Gradle, or any logging API as a
compile-time dependency — `jivl-core` (where this interface lives) has
none of those on its classpath, and this interface must not force one in
to be implementable.

### 10. `VerificationContext`

Immutable record/class carrying:

```
SafeRelativePath (or equivalent) skillRoot handle
java.time.Clock clock
String verifierVersion
```

- Immutable.
- Safe skill-root handling: the context exposes the skill root only
  through the same safe-path discipline as `SafeRelativePath` — no raw
  absolute-path leakage.
- `java.time.Clock` is injected (constructor parameter), never
  constructed internally as `Clock.systemUTC()` by rule implementations —
  this is what makes Phase 1A's tests deterministic per `AGENTS.md`'s
  "Clock injection... never `Instant.now()` directly in logic that is
  unit tested."
- No direct `Instant.now()` (or `LocalDateTime.now()`, etc.) usage
  anywhere in deterministic production logic that consumes
  `VerificationContext` — timestamps must go through the injected
  `Clock`.
- No credential, remote-provider-token, or telemetry fields of any kind —
  `VerificationContext` is pure execution context (skill root, clock,
  verifier version), never a place secrets could accidentally be threaded
  through.

## Security Considerations

- `Finding.evidence` is typed as `RedactedEvidence`, not a raw `String` —
  the type system itself, not just documentation, signals that
  unredacted text must not flow into a `Finding`. (Actual redaction logic
  is out of scope here — see item 6's explicit caveat.)
- Public models (`Finding`, `SafeRelativePath`, `VerificationContext`)
  must not contain or expose absolute local filesystem paths — enforced
  by `SafeRelativePath`'s design (item 5), not left to caller discipline.
- `SafeRelativePath` must reject path traversal (`..`, encoded traversal
  sequences, absolute paths) at construction time, ties to
  `docs/threat-model.md`'s directory-escape mitigation.
- None of the core types in this RFC execute skill content in any way —
  `Finding`, `VerificationRule`, and `VerificationContext` are pure data
  and orchestration contracts; no `ProcessBuilder`, `Runtime.exec`, or
  reflection-based invocation of skill-supplied content exists anywhere
  in this scope.
- `VerificationContext` must not fetch remote resources — it carries no
  network client, URL, or credential of any kind (see item 10's explicit
  "no credential/telemetry fields" requirement).
- No secret or credential fields exist anywhere in this RFC's types.
- `ERROR` must never be silently converted to `PASS` — `FindingStatus` is
  a closed enum with both values distinct, and nothing in this RFC's
  contracts allows collapsing one into the other; enforcement of this at
  the orchestration level is Phase 1C's concern (`docs/implementation-plan.md`),
  but the type system here does not make the mistake possible by
  accident (e.g. there is no boolean `success` field that could conflate
  the two).
- An unavailable check must be represented as `FindingStatus.SKIPPED`,
  never `PASS` — again enforced by `FindingStatus` being a closed,
  five-valued enum rather than a boolean, per `AGENTS.md`'s "Do not
  report an unavailable check as PASS."

## Compatibility Considerations

- No public release of JIVL exists yet (`0.1.0-SNAPSHOT`); there is no
  released API to break.
- Phase 1A APIs defined by this RFC may still evolve before `0.1.0` ships
  — this RFC governs the *initial* contract, not a frozen-forever one.
- Any future breaking change to the types defined here (renaming a field,
  changing the deterministic ordering, adding/removing an enum value)
  requires its own RFC review per `GOVERNANCE.md`'s "Rules for Changing
  Verification Policy," since `FindingCategory`/`FindingSeverity` changes
  are explicitly listed there as requiring an RFC.
- This RFC does **not** freeze RFC-0002's report schema. Nothing here
  constrains how `jivl-reporting` later serializes a `Finding` to JSON —
  that mapping, and any `schemaVersion` implications, remain entirely
  RFC-0002's (still-open) concern.
- Serialization compatibility (JSON shape, wire format, schema versioning)
  is explicitly out of scope for this RFC.

## Alternatives

- **Keep the Finding model embedded in RFC-0002.** Rejected (this is the
  governance decision this RFC implements): bundling forced accepting or
  rejecting the low-risk domain model together with the much larger,
  partially-unresolved report envelope. Splitting lets Phase 1A proceed
  without deciding the environment-summary question or freezing a JSON
  shape prematurely.
- **Partially accept RFC-0002** (e.g. an informal "Accepted for section X
  only" note on RFC-0002 itself, without extracting a new RFC). Rejected
  by the Lead Maintainer: `docs/rfcs/README.md`'s lifecycle does not
  define a partial-acceptance state, and inventing one informally would
  make RFC status ambiguous for every future RFC, not just this one.
  Extracting a cleanly-scoped new RFC (this document) keeps the
  `Accepted`/`Draft` distinction binary and unambiguous.
- **Use plain strings instead of value types** (raw `String` for rule
  IDs, file paths, and evidence). Rejected: this is exactly what
  `SafeRelativePath` and `RedactedEvidence` exist to prevent — a raw
  `String` file field could hold an absolute path or a traversal
  sequence, and a raw `String` evidence field could hold an unredacted
  secret, with nothing in the type system to catch either at construction
  time. Wrapping them in validated value types moves those invariants
  into the constructor, where they can't be forgotten per call site.
- **Expose `java.nio.file.Path` directly in report-facing models.**
  Rejected: `Path` values are frequently absolute, platform-dependent
  (Windows vs. POSIX separators, drive letters), and carry no built-in
  "this is a safe, skill-relative reference" guarantee — exactly the
  local-path-leak risk `docs/threat-model.md`'s Registry-Specific Threats
  section warns about. `SafeRelativePath` provides the same practical
  capability (referencing a file within the skill) without that risk.
- **Use a single numeric score** instead of categorized `Finding`s and
  `VerificationSummary` counts. Rejected, consistent with already-Accepted
  ADR-0003: a single score hides *why* a skill scored what it did, invites
  gaming a weighting formula, and cannot represent SKIPPED distinctly from
  PASS or FAIL. This RFC does not reopen that decision, only implements
  it at the type level.

## Open Questions

- The exact canonical `RuleId` regular expression (item 4) is deliberately
  not pinned down field-by-field in this RFC — it will be finalized during
  Phase 1A implementation, informed by the first real rule IDs
  `jivl-security`/structural-validation actually need, and recorded in
  code/tests rather than re-litigated here. This is an implementation
  detail within the already-Accepted contract, not a design question that
  blocks acceptance.
- Whether `VerificationRule`'s `List<Finding>` return type should later
  become a dedicated immutable `FindingList` wrapper type (e.g. to attach
  the deterministic-ordering guarantee at the type level rather than by
  convention) is left open for a future amendment if Phase 1B/1C
  implementation experience shows `List<Finding>` alone is insufficient.

## Decision History

- 2026-07-28: Initial extraction from RFC-0002's Finding-domain portion,
  proposed as part of the Phase 1 governance-review task, per the
  recommendation in `docs/rfcs/PHASE-1-RFC-REVIEW.md` (§2, "Cross-Cutting
  Note for Both RFCs").
- 2026-07-28: **Accepted by Nityam Jigyasu (@nmj18txstate), Lead
  Maintainer**, in this session. Accepted scope is limited strictly to
  Phase 1A as defined in the Design section above (and explicitly excluded
  by the Non-Goals section). Implementation has **not** started as of this
  acceptance — status is `Accepted`, not `Implemented`; `Implemented` will
  only be recorded once the corresponding `jivl-core` code and tests exist
  and `./mvnw -B -ntp clean verify` passes for them, per
  `docs/rfcs/README.md`'s lifecycle definition.
